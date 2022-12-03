# \[G‑01\] Functions guaranteed to revert when called by normal users can be marked payable

If a function modifier such as onlyOwner is used, the function will revert if a normal user tries to pay the function. Marking the function as payable will lower the gas cost for legitimate callers because the compiler will not include checks for whether a payment was provided. The extra opcodes avoided are
CALLVALUE(2),DUP1(3),ISZERO(3),PUSH2(3),JUMPI(10),PUSH1(3),DUP1(3),REVERT(0),JUMPDEST(1),POP(2), which costs an average of about 21 gas per call to the function, in addition to the extra deployment cost.

File: PositionMetadata.sol

function setBaseURI(string memory _baseURI) external onlyOwner {

- https://github.com/code-423n4/2022-12-Stealth-Project/blob/f985239db0ceb83c0c0931623607f603ad874a22/maverick-v1/contracts/models/PositionMetadata.sol#L17

File: Position.sol

function setMetadata(IPositionMetadata _metadata) external onlyOwner {

- https://github.com/code-423n4/2022-12-Stealth-Project/blob/f985239db0ceb83c0c0931623607f603ad874a22/maverick-v1/contracts/models/Position.sol#L23

# \[G-02\] Splitting require() statements that use && saves gas

See this issue which describes the fact that there is a larger deployment gas cost, but with enough runtime calls, the change ends up being cheaper by 3 gas:

File: Pool.sol

require((currentState.status & LOCKED == 0) && (allowInEmergency || (currentState.status & EMERGENCY == 0)), "E");

- https://github.com/code-423n4/2022-12-Stealth-Project/blob/f985239db0ceb83c0c0931623607f603ad874a22/maverick-v1/contracts/models/Pool.sol#L82


File: Factory.sol

 require(msg.sender == owner && _owner != address(0), "Factory:NOT_ALLOWED");

require(_fee > 0 && _fee < 1e18, "Factory:FEE_OUT_OF_BOUNDS");

require(_lookback >= 3600 && _lookback <= uint16(type(int16).max), "Factory:LOOKBACK_OUT_OF_BOUNDS");

- https://github.com/code-423n4/2022-12-Stealth-Project/blob/1fd474ff560404b7ea4ac4c492397a4571481814/maverick-v1/contracts/models/Factory.sol#L41

- https://github.com/code-423n4/2022-12-Stealth-Project/blob/1fd474ff560404b7ea4ac4c492397a4571481814/maverick-v1/contracts/models/Factory.sol#L60

- https://github.com/code-423n4/2022-12-Stealth-Project/blob/1fd474ff560404b7ea4ac4c492397a4571481814/maverick-v1/contracts/models/Factory.sol#L62

# [G-03] x += y COSTS MORE GAS THAN x= x + y FOR STATE VARIABLES

Using the addition operator instead of plus-equals saves 113 gas

File: Pool.sol

tokenAAmount += temp.deltaA;
 
tokenBAmount += temp.deltaB;

binBalanceA += tokenAAmount.toUint128();

binBalanceB += tokenBAmount.toUint128();

bin.balances[toTokenId] += param.amount;

bin.balances[fromTokenId] -= param.amount;

tokenAOut += deltaA;

tokenBOut += deltaB;

- https://github.com/code-423n4/2022-12-Stealth-Project/blob/f985239db0ceb83c0c0931623607f603ad874a22/maverick-v1/contracts/models/Pool.sol#L120

- https://github.com/code-423n4/2022-12-Stealth-Project/blob/f985239db0ceb83c0c0931623607f603ad874a22/maverick-v1/contracts/models/Pool.sol#L121

- https://github.com/code-423n4/2022-12-Stealth-Project/blob/f985239db0ceb83c0c0931623607f603ad874a22/maverick-v1/contracts/models/Pool.sol#L131

- https://github.com/code-423n4/2022-12-Stealth-Project/blob/f985239db0ceb83c0c0931623607f603ad874a22/maverick-v1/contracts/models/Pool.sol#L132

- https://github.com/code-423n4/2022-12-Stealth-Project/blob/f985239db0ceb83c0c0931623607f603ad874a22/maverick-v1/contracts/models/Pool.sol#L169

- https://github.com/code-423n4/2022-12-Stealth-Project/blob/f985239db0ceb83c0c0931623607f603ad874a22/maverick-v1/contracts/models/Pool.sol#L170

- https://github.com/code-423n4/2022-12-Stealth-Project/blob/f985239db0ceb83c0c0931623607f603ad874a22/maverick-v1/contracts/models/Pool.sol#L195

- https://github.com/code-423n4/2022-12-Stealth-Project/blob/f985239db0ceb83c0c0931623607f603ad874a22/maverick-v1/contracts/models/Pool.sol#L196

# [G-04]  ++I/I++ SHOULD BE UNCHECKED{++I}/UNCHECKED{I++} WHEN IT IS NOT POSSIBLE FOR THEM TO OVERFLOW, AS IS THE CASE WHEN USED IN FOR- AND WHILE-LOOPS

 

The unchecked keyword is new in solidity version 0.8.0, so this only applies to that version or higher, which these instances are. This saves 30-40 gas per loop:

File: Pool.sol

for (uint256 i; i < params.length; i++) {

for (uint256 i; i < binIds.length; i++) {

 for (uint256 i; i < params.length; i++) {

 for (uint256 i; i < params.length; i++) {

for (uint256 i; i <= moveData.binCounter; i++) {

for (uint256 i; i < moveData.mergeBinCounter; i++) {

- https://github.com/code-423n4/2022-12-Stealth-Project/blob/f985239db0ceb83c0c0931623607f603ad874a22/maverick-v1/contracts/models/Pool.sol#L112

- https://github.com/code-423n4/2022-12-Stealth-Project/blob/f985239db0ceb83c0c0931623607f603ad874a22/maverick-v1/contracts/models/Pool.sol#L150

- https://github.com/code-423n4/2022-12-Stealth-Project/blob/f985239db0ceb83c0c0931623607f603ad874a22/maverick-v1/contracts/models/Pool.sol#L163

- https://github.com/code-423n4/2022-12-Stealth-Project/blob/f985239db0ceb83c0c0931623607f603ad874a22/maverick-v1/contracts/models/Pool.sol#L190

- https://github.com/code-423n4/2022-12-Stealth-Project/blob/f985239db0ceb83c0c0931623607f603ad874a22/maverick-v1/contracts/models/Pool.sol#L353

- https://github.com/code-423n4/2022-12-Stealth-Project/blob/f985239db0ceb83c0c0931623607f603ad874a22/maverick-v1/contracts/models/Pool.sol#L378

