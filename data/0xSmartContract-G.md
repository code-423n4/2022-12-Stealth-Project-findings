### Gas Optimizations List
| Number | Optimization Details | Context |
|:--:|:-------| :-----:|
| [G-01] | `Instance` struct can be packed into fewer slots | 1 |
| [G-02] |Using `delete` instead of setting `binPositions` mapping to 0 saves gas | 1 |
| [G-03] | Use `nested if` and, avoid multiple check combinations | 2 |
| [G-04] | Use ``assembly`` to write _address storage values_  | 8 |
| [G-05] | Setting the constructor to `payable` | 5|
| [G-06] | Functions guaranteed to revert_ when callled by normal users can be marked `payable`   | 5 |
| [G-07] | Use ``double require`` instead of using ``&&``   | 12 |
| [G-08] | Sort solidity operations using `short-circuit mode` | 17 |
| [G-09] | Optimize names to save gas  | All contracts |
| [G-10] | ```x -= y  (x += y )``` costs more gas than ```x = x – y  (x = x + y) ``` for state variables  | 14 |
| [G-11] | Use a different pattern when using for loops  |14 |
| [G-12] | The solady Library's Ownable contract is significantly gas-optimized, which can be used  | 2 |
| [G-13] | Comparison operators  | 10 |
| [G-14] |Use `Solmate SafeTransferLib ` contracts  |  |

Total 14 issues

### Suggestions
| Number | Suggestion Details |
|:--:|:-------|
| [S-01] |Use `v4.8.0 OpenZeppelin` contracts |

Total 1 suggestion


### [G-01] `Instance` struct can be packed into fewer slots

```solidity

   5:     struct Instance {
   6         uint256 deltaInBinInternal;        // slot 0
   7         uint256 deltaInErc;                // slot 1
   8         uint256 deltaOutErc;               // slot 2
   9         uint256 excess;		        // slot 3
  10         bool tokenAIn;		        // slot 4
  11         uint256 endSqrtPrice;		// slot 5
  12         bool exactOutput;     	        // slot 6
  13         bool swappedToMaxPrice;       	// slot 6
  14         bool skipCombine;		        // slot 6
  15         bool decrementTick;		// slot 6
  16         uint256 sqrtPriceLimit; 		// slot 7
  17         uint256 sqrtLowerTickPrice;	// slot 8
  18         uint256 sqrtUpperTickPrice;	// slot 9
  19         uint256 sqrtPrice;		        // slot 10
  20     }
```
https://github.com/code-423n4/2022-12-Stealth-Project/blob/main/maverick-v1/contracts/libraries/Delta.sol#L5-L20

The `Instance` struct in the delta.sol contract can be packed into one slot less slot as suggested below.

```diff
maverick-v1\contracts\libraries\Delta.sol:
   5:     struct Instance {
   6         uint256 deltaInBinInternal;	// slot 0
   7         uint256 deltaInErc;	        // slot 1
   8         uint256 deltaOutErc;		// slot 2
   9         uint256 excess;		        // slot 3
+ 11         uint256 endSqrtPrice;		// slot 4
- 10         bool tokenAIn;		
- 11         uint256 endSqrtPrice;		
+ 10        bool tokenAIn;		        // slot 5
  12         bool exactOutput;		        // slot 5
  13         bool swappedToMaxPrice;       	// slot 5
  14         bool skipCombine;		        // slot 5
  15         bool decrementTick;		// slot 5
  16         uint256 sqrtPriceLimit; 		// slot 6
  17         uint256 sqrtLowerTickPrice;	// slot 7
  18         uint256 sqrtUpperTickPrice;	// slot 8
  19         uint256 sqrtPrice;		        // slot 9
  20     }
```

### [G-02] Using `delete` instead of setting `binPositions` mapping to 0 saves gas

```diff
maverick-v1\contracts\models\Pool.sol:
   358     function _removeBin(int32 tick, uint8 kind) internal {
- 359:         binPositions[tick][kind] = 0;
+ 359:         delete binPositions[tick][kind];
   360         binMap.removeTypeAtTick(kind, tick);
   361     }

```

https://github.com/code-423n4/2022-12-Stealth-Project/blob/main/maverick-v1/contracts/models/Pool.sol#L359


### [G-03] Use nested if and, avoid multiple check combinations

Using nested is cheaper than using && multiple check combinations. There are more advantages, such as easier to read code and better coverage reports.

```solidity
2 results 2 files

maverick-v1\contracts\models\Pool.sol:
  233:             if (bin.state.reserveA == 0 && bin.state.reserveB == 0 && isActive) {

router-v1\contracts\Router.sol:
  89:         if (IWETH9(address(token)) == WETH9 && address(this).balance >= value) {
```
https://github.com/code-423n4/2022-12-Stealth-Project/blob/main/maverick-v1/contracts/models/Pool.sol#L233
https://github.com/code-423n4/2022-12-Stealth-Project/blob/main/router-v1/contracts/Router.sol#L89

**Recomendation Code:**

```diff
maverick-v1\contracts\models\Pool.sol#L233

- if (bin.state.reserveA == 0 && bin.state.reserveB == 0 && isActive) {
                _removeBin(bin.state.lowerTick, bin.state.kind);
                isActive = false;
            }

+ if (bin.state.reserveA == 0) {
+            if (bin.state.reserveB == 0) {
+                if (isActive) {
                     _removeBin(bin.state.lowerTick, bin.state.kind);
                     isActive = false;
                }
             }   
        }
```


### [G-04] Use ``assembly`` to write _address storage values_ 

```solidity
8 results 4 files

maverick-v1\contracts\models\Factory.sol:
  26     constructor(uint16 _protocolFeeRatio, IPosition _position) {
  30:         position = _position;
  31:     }

  40     function setOwner(address _owner) external {
  41         require(msg.sender == owner && _owner != address(0), "Factory:NOT_ALLOWED");
  42:         owner = _owner;

maverick-v1\contracts\models\Pool.sol:
  60     constructor(
  78:         tokenA = _tokenA;
  79:         tokenB = _tokenB;

maverick-v1\contracts\models\Position.sol: 
  18     constructor(IPositionMetadata _metadata) ERC721("Maverick Position NFT", "MPN") {
  19:         metadata = _metadata;
  20         _tokenIdCounter.increment();
  21     }

router-v1\contracts\Router.sol:
  48     constructor(IFactory _factory, IPosition _position, IWETH9 _WETH9) {
  49:         factory = _factory;
  50:         position = _position;
  51:         WETH9 = _WETH9;
  52     }

```
https://github.com/code-423n4/2022-12-Stealth-Project/blob/main/maverick-v1/contracts/models/Factory.sol#L30
https://github.com/code-423n4/2022-12-Stealth-Project/blob/main/maverick-v1/contracts/models/Factory.sol#L42
https://github.com/code-423n4/2022-12-Stealth-Project/blob/main/maverick-v1/contracts/models/Pool.sol#L78-L79
https://github.com/code-423n4/2022-12-Stealth-Project/blob/main/maverick-v1/contracts/models/Position.sol#L19
https://github.com/code-423n4/2022-12-Stealth-Project/blob/main/router-v1/contracts/Router.sol#L49-L51



### [G-05] Setting the constructor to `payable`

You can cut out 10 opcodes in the creation-time EVM bytecode if you declare a constructor payable. Making the constructor payable eliminates the need for an initial check of ```msg.value == 0``` and saves ```13 gas``` on deployment with no security risks.

```solidity
5 results 5 files

maverick-v1\contracts\models\Factory.sol:
  26:     constructor(uint16 _protocolFeeRatio, IPosition _position) {

maverick-v1\contracts\models\Pool.sol:
  60:     constructor(

maverick-v1\contracts\models\Position.sol:
  18:     constructor(IPositionMetadata _metadata) ERC721("Maverick Position NFT", "MPN") {

maverick-v1\contracts\models\PositionMetadata.sol:
  13:     constructor(string memory _baseURI) {

router-v1\contracts\Router.sol:
  48:     constructor(IFactory _factory, IPosition _position, IWETH9 _WETH9) {

```

https://github.com/code-423n4/2022-12-Stealth-Project/blob/main/maverick-v1/contracts/models/Factory.sol#L26
https://github.com/code-423n4/2022-12-Stealth-Project/blob/main/maverick-v1/contracts/models/Pool.sol#L60-L71
https://github.com/code-423n4/2022-12-Stealth-Project/blob/main/maverick-v1/contracts/models/Position.sol#L18
https://github.com/code-423n4/2022-12-Stealth-Project/blob/main/maverick-v1/contracts/models/Position.sol#L18
https://github.com/code-423n4/2022-12-Stealth-Project/blob/main/router-v1/contracts/Router.sol#L48

**Recommendation:**
Set the constructor to ```payable```

### [G-06]  Functions guaranteed to revert_ when callled by normal users can be marked `payable` 

If a function modifier or require such as onlyOwner-admin is used, the function will revert if a normal user tries to pay the function. Marking the function as payable will lower the gas cost for legitimate callers because the compiler will not include checks for whether a payment was provided. The extra opcodes avoided are CALLVALUE(2), DUP1(3), ISZERO(3), PUSH2(3), JUMPI(10), PUSH1(3), DUP1(3), REVERT(0), JUMPDEST(1), POP(2) which costs an average of about 21 gas per call to the function, in addition to the extra deployment cost.

```solidity
5 results- 4 files

maverick-v1\contracts\models\Factory.sol:
  33:     function setProtocolFeeRatio(uint16 _protocolFeeRatio) external {
  40:     function setOwner(address _owner) external {

maverick-v1\contracts\models\Position.sol:
  23:     function setMetadata(IPositionMetadata _metadata) external onlyOwner {

maverick-v1\contracts\models\PositionMetadata.sol:
  17:     function setBaseURI(string memory _baseURI) external onlyOwner {

maverick-v1\contracts\models\Pool.sol:
  339:     function adminAction(uint256 action, uint16 val, address recipient) external {

```

**Recommendation:**
Functions guaranteed to revert when called by normal users can be marked payable  (for only ```onlyAssetListingOrPoolAdmins, onlyWhenAssetExisted, onlyWhenAssetNotExisted, onlyWhenFeederExisted, onlyWhenFeederNotExisted or onlyRole``` functions)

### [G-07] Use ``double require`` instead of using ``&&``

Using double require instead of operator && can save more gas
When having a require statement with 2 or more expressions needed, place the expression that cost less gas first.


```solidity
12 results - 4 files

maverick-v1\contracts\models\Factory.sol:
  41:         require(msg.sender == owner && _owner != address(0), "Factory:NOT_ALLOWED");
  60:         require(_fee > 0 && _fee < 1e18, "Factory:FEE_OUT_OF_BOUNDS");
  62:         require(_lookback >= 3600 && _lookback <= uint16(type(int16).max), "Factory:LOOKBACK_OUT_OF_BOUNDS");

maverick-v1\contracts\models\Pool.sol:
   93:         require((currentState.status & LOCKED == 0) && (allowInEmergency || (currentState.status & EMERGENCY == 0)), "E");
  168:         require(previousABalance + tokenAAmount <= _tokenABalance() && previousBBalance + tokenBAmount <= _tokenBBalance(), "A");

router-v1\contracts\Router.sol:
  100:         require(amountToPay > 0 && amountOut > 0, "In or Out Amount is Zero");
  234:         require(tokenAAmount >= minTokenAAmount && tokenBAmount >= minTokenBAmount, "Too little added");
  262:         require(activeTick >= minActiveTick && activeTick <= maxActiveTick, "activeTick not in range");
  309:         require(tokenAAmount >= minTokenAAmount && tokenBAmount >= minTokenBAmount, "Too little removed");

router-v1\contracts\libraries\TransferHelper.sol:
  15:         require(success && (data.length == 0 || abi.decode(data, (bool))), "STF");
  25:         require(success && (data.length == 0 || abi.decode(data, (bool))), "ST");
  35:         require(success && (data.length == 0 || abi.decode(data, (bool))), "SA");
```

**Current Code:**

maverick-v1\contracts\models\Factory.sol:L#41;
 ```solidity
   require(msg.sender == owner && _owner != address(0), "Factory:NOT_ALLOWED");
```

Recommendation Code:
```solidity
     require(msg.sender == owner, "Not Authorized");
     require(_owner != address(0), "Factory:NOT_ALLOWED");
```

### [G-08] Sort Solidity operations using short-circuit mode

Short-circuiting is a solidity contract development model that uses ```OR/AND``` logic to sequence different cost operations. It puts low gas cost operations in the front and high gas cost operations in the back, so that if the front is low If the cost operation is feasible, you can skip (short-circuit) the subsequent high-cost Ethereum virtual machine operation.
```solidity
//f(x) is a low gas cost operation 
//g(y) is a high gas cost operation 

//Sort operations with different gas costs as follows 
f(x) || g(y) 
f(x) && g(y)
```

```solidity
17 results - 5 files

maverick-v1\contracts\libraries\Bin.sol:
  35:         if (self.state.lowerTick < _activeLowerTick || (!noA && noB)) {
  38:         } else if (self.state.lowerTick > _activeLowerTick || (noA && !noB)) {
  45:             if (deltaBOptimal > _deltaB && _existingReserveB > 0) {
  50:             proRataLiquidity = (noA && noB) || self.state.totalSupply == 0

maverick-v1\contracts\models\Factory.sol:
  41:         require(msg.sender == owner && _owner != address(0), "Factory:NOT_ALLOWED");
  60:         require(_fee > 0 && _fee < 1e18, "Factory:FEE_OUT_OF_BOUNDS");
  62:         require(_lookback >= 3600 && _lookback <= uint16(type(int16).max), "Factory:LOOKBACK_OUT_OF_BOUNDS");

maverick-v1\contracts\models\Pool.sol:
   93:         require((currentState.status & LOCKED == 0) && (allowInEmergency || (currentState.status & EMERGENCY == 0)), "E");
  168:         require(previousABalance + tokenAAmount <= _tokenABalance() && previousBBalance + tokenBAmount <= _tokenBBalance(), "A");

router-v1\contracts\Router.sol:
   89:         if (IWETH9(address(token)) == WETH9 && address(this).balance >= value) {
  100:         require(amountToPay > 0 && amountOut > 0, "In or Out Amount is Zero");
  234:         require(tokenAAmount >= minTokenAAmount && tokenBAmount >= minTokenBAmount, "Too little added");
  262:         require(activeTick >= minActiveTick && activeTick <= maxActiveTick, "activeTick not in range");
  309:         require(tokenAAmount >= minTokenAAmount && tokenBAmount >= minTokenBAmount, "Too little removed");

router-v1\contracts\libraries\TransferHelper.sol:
  15:         require(success && (data.length == 0 || abi.decode(data, (bool))), "STF");
  25:         require(success && (data.length == 0 || abi.decode(data, (bool))), "ST");
  35:         require(success && (data.length == 0 || abi.decode(data, (bool))), "SA");
```


### [G-09] Optimize names to save gas
Contracts most called functions could simply save gas by function ordering via ```Method ID```. Calling a function at runtime will be cheaper if the function is positioned earlier in the order (has a relatively lower Method ID) because ```22 gas``` are added to the cost of a function for every position that came before it. The caller can save on gas if you prioritize most called functions. 

**Context:** 
All Contracts


**Recommendation:** 
Find a lower ```method ID``` name for the most called functions for example Call() vs. Call1() is cheaper by ```22 gas```
For example, the function IDs in the ``` Pool.sol ``` contract will be the most used; A lower method ID may be given.

**Proof of Consept:**
https://medium.com/joyso/solidity-how-does-function-name-affect-gas-consumption-in-smart-contract-47d270d8ac92

Pool.sol function names can be named and sorted according to METHOD ID

```js
Sighash   |   Function Signature
========================
8031aa9c  =>  checkPositionAccess(uint256)
139cd3af  =>  checkReentrancy(bool,bool)
1865c57d  =>  getState()
a4ed496a  =>  getTwa()
4af8222c  =>  addLiquidity(uint256,AddLiquidityParams[],bytes)
a151ee34  =>  migrateBinsUpStack(uint128[],uint32)
334fe6ca  =>  transferLiquidity(uint256,uint256,RemoveLiquidityParams[])
6b65e688  =>  removeLiquidity(address,uint256,RemoveLiquidityParams[])
c51c9029  =>  swap(address,uint256,bool,bool,uint256,bytes)
44a185bb  =>  getBin(uint128)
6da3bf8b  =>  balanceOf(uint256,uint128)
a9f6a493  =>  claimProtocolFees(address,bool)
f37d1cb4  =>  setProtocolFeeRatio(uint16)
411cf411  =>  adminAction(uint256,uint16,address)
5dc7c983  =>  _removeBin(int32,uint8)
73e5e524  =>  _moveDirection(int32,MoveData)
cd592349  =>  _moveBins(int32,int32,int32)
9fcd54c5  =>  _getOrCreateBin(State,uint8,int32,bool)
dd151592  =>  _tokenABalance()
f9df64df  =>  _tokenBBalance()
ecc7c646  =>  _currentTickLiquidity(int32)
1da710fc  =>  _firstKindAtTickLiquidity(int32)
ebc636c2  =>  _amountToBin(uint256,uint256)
fa640e50  =>  _computeSwapExactIn(uint256,uint256,uint256,uint256,uint256,uint256,bool,bool)
62b5ba74  =>  _computeSwapExactOut(uint256,uint256,uint256,uint256,uint256,bool)
0f01070d  =>  _swapTick(State,Delta.Instance)
```


### [G-10] ```x -= y  (x += y )``` costs more gas than ```x = x – y  (x = x + y) ``` for state variables

```solidity
14 results  3 files

maverick-v1\contracts\libraries\Bin.sol:
  114:             maxRecursion -= 1;
  142:             self.balances[tokenId] -= deltaLpBalance128;
  144:             self.state.totalSupply -= deltaLpBalance128;
  148:             self.state.mergeBinBalance -= deltaLpBalance128;
  154:         activeBin.state.totalSupply -= deltaLpBalance128;
  157:             activeBin.balances[tokenId] -= deltaLpBalance128;

maverick-v1\contracts\libraries\BinMath.sol:
  53:                 result -= 128;
  58:                 result -= 64;
  63:                 result -= 32;
  68:                 result -= 16;
  73:                 result -= 8;
  78:                 result -= 4;
  83:                 result -= 2;

maverick-v1\contracts\models\Pool.sol:
  200:             bin.balances[fromTokenId] -= param.amount;
```
```x -= y``` costs more gas than ```x = x – y``` for state variables.

```solidity
37 results  6 files

maverick-v1\contracts\libraries\Bin.sol:
  67:             self.state.reserveA += deltaIn;
  70:             self.state.reserveB += deltaIn;
  88:         self.state.totalSupply += deltaLpToken128;
  89:         self.balances[tokenId] += deltaLpToken128;
  90:         self.state.reserveA += deltaA.toUint128();
  91:         self.state.reserveB += deltaB.toUint128();

maverick-v1\contracts\libraries\BinMap.sol:
  54:                 offset += bitIndex;
  66:                 counter += 1;
  68:                 offset += uint16(uint32(NUMBER_OF_KINDS_32));
  70:             wordIndex += 1;
  97:             mapIndex += tack;

maverick-v1\contracts\libraries\BinMath.sol:
  18:                 result += 128;
  22:                 result += 64;
  26:                 result += 32;
  30:                 result += 16;
  34:                 result += 8;
  38:                 result += 4;
  42:                 result += 2;

maverick-v1\contracts\libraries\Delta.sol:
  24:             self.deltaInBinInternal += delta.deltaInBinInternal;
  25:             self.deltaInErc += delta.deltaInErc;
  26:             self.deltaOutErc += delta.deltaOutErc;

maverick-v1\contracts\models\Pool.sol:
  142:             tokenAAmount += temp.deltaA;
  143:             tokenBAmount += temp.deltaB;
  161:         binBalanceA += tokenAAmount.toUint128();
  162:         binBalanceB += tokenBAmount.toUint128();
  199:             bin.balances[toTokenId] += param.amount;
  229:             tokenAOut += deltaA;
  230:             tokenBOut += deltaB;
  288:                 binBalanceA += delta.deltaInBinInternal.toUint128();
  291:                 binBalanceB += delta.deltaInBinInternal.toUint128();
  422:                 moveData.totalReserveA += bin.state.reserveA;
  423:                 moveData.totalReserveB += bin.state.reserveB;
  491:             currentState.binCounter += 1;
  527:                 output.currentReserveA += bin.state.reserveA;
  528:                 output.currentReserveB += bin.state.reserveB;

maverick-v1\contracts\models\PoolInspector.sol:
  103:             reserveA += bin.reserveA;
  104:             reserveB += bin.reserveB;
```
```x += y``` costs more gas than ```x = x + y``` for state variables.

### [G-11] Use a different pattern when using for loops

In the use of for loops, this structure, which will reduce gas costs, can be preferred. It saves approximately 400 gas in an array with 6 members.

```solidity
14 results - 3 files

maverick-v1\contracts\models\Pool.sol:
  127:         for (uint256 i; i < params.length; i++) {
  128              AddLiquidityParams memory param = params[i];

  179          checkReentrancy(true, false);
  180:         for (uint256 i; i < binIds.length; i++) {
  181              Bin.Instance storage bin = bins[binIds[i]];

  192          checkPositionAccess(fromTokenId);
  193:         for (uint256 i; i < params.length; i++) {
  194              RemoveLiquidityParams memory param = params[i];

  224:         for (uint256 i; i < params.length; i++) {
  225              RemoveLiquidityParams memory param = params[i];

  380:         for (uint256 j; j < 2; j++) {
  381              uint8 kind = moveData.kinds[j];

  389:             for (uint256 i; i <= moveData.binCounter; i++) {
  390                  BinMap.Active memory active = moveData.activeList[i];

  414:             for (uint256 i; i < moveData.mergeBinCounter; i++) {
  415                  uint128 binId = moveData.mergeBins[i];

  523:         for (uint256 i; i < NUMBER_OF_KINDS; i++) {
  524              if ((word & (1 << i)) != 0) {

  540:         for (uint256 i; i < NUMBER_OF_KINDS; i++) {
  541              if ((word & (1 << i)) != 0) {

  653:             for (uint256 i; i < swapData.counter; i++) {
  654                  Bin.Instance storage bin = bins[currentBinIds[i]];

maverick-v1\contracts\models\PoolInspector.sol:
   29          uint128 activeCounter = 0;
   30:         for (uint128 i = startBinIndex; i < binCounter; i++) {
   31              IPool.BinState memory bin = pool.getBin(i + 1);

   79          bins = new IPool.BinState[](binCounter);
   80:         for (uint8 i = 0; i < NUMBER_OF_KINDS; i++) {
   81              uint128 binId = pool.binPositions(tick, i);

  101:         for (uint256 i; i < bins.length; i++) {
  102              IPool.BinState memory bin = bins[i];

router-v1\contracts\libraries\Multicall.sol:
  12          results = new bytes[](data.length);
  13:         for (uint256 i = 0; i < data.length; i++) {
  14              (bool success, bytes memory result) = address(this).delegatecall(data[i]);
```

**Recommendation Code:**

maverick-v1\contracts\models\Pool.sol#L127
```diff
- 127:         for (uint256 i; i < params.length; i++) {
+ 127:         for (uint256 i; i < params.length; i = unchecked_inc(i)) {
                   // do something that doesn't change the value of 
  128              AddLiquidityParams memory param = params[i];

+ function unchecked_inc(uint i) internal pure returns (uint) {
+       unchecked {
+            return i + 1;
+        }
+  }
```


### [G-12] The solady Library's Ownable contract is significantly gas-optimized, which can be used

The project uses the ` onlyOwner ` authorization model I recommend using _Solady's highly gas optimized contract._

https://github.com/Vectorized/solady/blob/main/src/auth/OwnableRoles.sol

```solidity
2 results - 2 files

maverick-v1\contracts\models\Position.sol:
  22  
  23:     function setMetadata(IPositionMetadata _metadata) external onlyOwner {
  24          metadata = _metadata;

maverick-v1\contracts\models\PositionMetadata.sol:
  16  
  17:     function setBaseURI(string memory _baseURI) external onlyOwner {
  18          baseURI = _baseURI;
```

### [G-13] Comparison operators

In the EVM, there is no opcode for `>=` or `<=`. When using greater than or equal, two operations are performed: `> and =`.
 Comment
Using strict comparison operators hence saves gas

```solidity
10 results - 5 files

maverick-v1\contracts\libraries\BinMath.sol:
  16:             if (x >= 0x100000000000000000000000000000000) {
  20:             if (x >= 0x10000000000000000) {
  24:             if (x >= 0x100000000) {
  28:             if (x >= 0x10000) {
  32:             if (x >= 0x100) {
  36:             if (x >= 0x10) {
  40:             if (x >= 0x4) {
  44:             if (x >= 0x2) result += 1;

maverick-v1\contracts\libraries\Twa.sol:
  29:         if (_timeDiff >= _lookback) {

maverick-v1\contracts\models\Factory.sol:
  62:         require(_lookback >= 3600 && _lookback <= uint16(type(int16).max), "Factory:LOOKBACK_OUT_OF_BOUNDS");

maverick-v1\contracts\models\Pool.sol:
  569:         if (amountIn.mul(PRBMathUD60x18.SCALE - fee) >= binAmountIn) {

router-v1\contracts\libraries\BytesLib.sol:
  13:         require(_length + 31 >= _length, "slice_overflow");
  75:         require(_start + 20 >= _start, "toAddress_overflow");
  76:         require(_bytes.length >= _start + 20, "toAddress_outOfBounds");
  87:         require(_start + 3 >= _start, "toUint24_overflow");
  88:         require(_bytes.length >= _start + 3, "toUint24_outOfBounds");

router-v1\contracts\libraries\Path.sol:
  28:         return path.length >= MULTIPLE_POOLS_MIN_LENGTH;
```

**Recommendation:** 
Replace <= with <, and >= with >. Do not forget to increment/decrement the compared variable.

```diff
router-v1\contracts\libraries\Path.sol:
- 28:         return path.length >= MULTIPLE_POOLS_MIN_LENGTH;
+ 28:         return path.length > MULTIPLE_POOLS_MIN_LENGTH - 1;
```

### [G-14] Use `Solmate SafeTransferLib ` contracts

**Description:**
Use the gas-optimized Solmate SafeTransferLib contract for Erc20

https://github.com/transmissions11/solmate/blob/main/src/utils/SafeTransferLib.sol


### [S-01] Use `v4.8.0 OpenZeppelin` contracts

**Description:**
The upcoming v4.8.0 version of OpenZeppelin provides many small gas optimizations.

https://github.com/OpenZeppelin/openzeppelin-contracts/releases/tag/v4.8.0-rc.0

```js
v4.8.0-rc.0
⛽ Many small optimizations
```