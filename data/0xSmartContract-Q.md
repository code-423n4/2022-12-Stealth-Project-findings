## Summary
### Low Risk Issues List
| Number |Issues Details|Context|
|:--:|:-------|:--:|
|[L-01]| Danger "while" loop| 1 |
|[L-02]| Draft Openzeppelin Dependencies | 1 |
|[L-03]| It is safer to change the `owner` address with the `safeSetOwner` pattern| 1 |
|[L-04]| There is a risk that the `proxyFee` variable is accidentally initialized to 0 and platform loses money| 2 |
|[L-05]|Use ```safeTransferOwnership``` instead of ```transferOwnership``` function| 1 |
|[L-06]| Owner can renounce Ownership | 2 |
|[L-07]|Missing Event for critical parameters change | 1 |
|[L-08]|A single point of failure | 1 |
|[L-09]|Some ERC20 tokens should need to approve(0) first| 1 |
|[L-10]|Not using the latest version of `prb-math` from dependencies| 1 |
|[L-11]|Loss of precision due to rounding| 1 |

Total 11 issues


### Non-Critical Issues List
| Number |Issues Details|Context|
|:--:|:-------|:--:|
| [N-01]|Critical Address Changes Should Use Two-step Procedure| 1 |
| [N-02] |Initial value check is missing in Set Functions  |4|
| [N-03] |Critical dependencies such as OpenZeppelin should use the latest version| 1 |
| [N-04] |Not using the named return variables anywhere in the function is confusing | 4 |
| [N-05] |Use a single file for all system-wide constants | 1|
| [N-06] |NatSpec comments should be increased in contracts | All Contracts |
| [N-07] |For functions, follow Solidity standard naming conventions| All Contracts |
| [N-08] |`Function writing` that does not comply with the `Solidity Style Guide` | All Contracts |
| [N-09] |Add a timelock to critical functions | 5 |
| [N-10] |Use a more recent version of Solidity|  All contracts  |
| [N-11] |Solidity compiler optimizations can be problematic|  |
| [N-12] |Insufficient coverage | 1 |
| [N-13] |For modern and more readable code; update import usages | 128 |
| [N-14] |`WETH` address definition can be use _directly_| 1 |
| [N-15] |Floating pragma | 1 |
| [N-16] |Add to indexed parameter for countable Events| 1 |
| [N-17] |Include return parameters in NatSpec comments | All Contracts |
| [N-18] |Omissions in Events| 4 |
| [N-19] |Long lines are not suitable for the `Solidity Style Guide` | 27 |
| [N-20] |`abicoder v2` is enabled by default| 1 |
| [N-21] |Need Fuzzing test| 2 |
| [N-22] |Take advantage of Custom Error's return value property| 1 |
| [N-23] | Some `require` descriptions are not clear | 5 |

Total 23 issues


### Suggestions
| Number | Suggestion Details |
|:--:|:-------|
| [S-01] |Make the Test Context with Solidity |
| [S-02] |Generate perfect code headers every time |

Total 2 suggestions


### [L-01] Danger "while" loop


```solidity

router-v1/contracts/Router.sol:
  142      /// @inheritdoc IRouter
  143:     function exactInput(ExactInputParams memory params) external payable override checkDeadline(params.deadline) returns (uint256 amountOut) {
  144:         address payer = msg.sender;
  145: 
  146:         while (true) {
  147:             bool stillMultiPoolSwap = params.path.hasMultiplePools();
  148: 
  149:             params.amountIn = exactInputInternal(
  150:                 params.amountIn,
  151:                 stillMultiPoolSwap ? address(this) : params.recipient,
  152:                 0,
  153:                 SwapCallbackData({path: params.path.getFirstPool(), payer: payer, exactOutput: false})
  154:             );
  155: 
  156:             if (stillMultiPoolSwap) {
  157:                 payer = address(this);
  158:                 params.path = params.path.skipToken();
  159:             } else {
  160:                 amountOut = params.amountIn;
  161:                 break;
  162:             }
  163:         }
  164: 
  165:         require(amountOut >= params.amountOutMinimum, "Too little received");
  166:     }

```

Firstly, the conditional part is evaluated

a) if the condition evaluates to true , the instructions inside the loop (defined inside the brackets { ... }) get executed.

b) once the code reaches the end of the loop body (= the closing curly brace } , it will jump back up to the conditional part).

c) the code inside the while loop body will keep executing over and over again until the conditional part turns false.

For loops are more associated with iterations. While loop instead are more associated with repetitive tasks. Such tasks could potentially be infinite if the while loop body never affects the condition initially checked.
Therefore, they should be used with care.


Because it is inside the `exactInput()` function, which is an external function, a DOS attack can create spam and break the function, especially in shallow liquidity.

### [L-02] Draft Openzeppelin Dependencies

The `SelfPermit.sol` contract utilised draft-IERC20Permit.sol, an OpenZeppelin contract. This contract is still a draft and is not considered ready for mainnet use. OpenZeppelin contracts may be considered draft contracts if they have not received adequate security auditing or are liable to change with future development.

[SelfPermit.sol#L5](https://github.com/code-423n4/2022-12-Stealth-Project/blob/main/router-v1/contracts/libraries/SelfPermit.sol#L5)

```solidity
router-v1/contracts/libraries/SelfPermit.sol:
  1: // SPDX-License-Identifier: GPL-2.0-or-later

  5: import "@openzeppelin/contracts/token/ERC20/extensions/draft-IERC20Permit.sol";
```
### [L-03] It is safer to change the `owner` address with the `safeSetOwner` pattern

```solidity
maverick-v1/contracts/models/Factory.sol:
  39  
  40:     function setOwner(address _owner) external {
  41:         require(msg.sender == owner && _owner != address(0), "Factory:NOT_ALLOWED");
  42:         owner = _owner;
  43:         emit SetFactoryOwner(_owner);
  44:     }

```


**Description:**
The `owner` address change is done with the `setOwner` function, but there is a risk of incorrect address definition here, it is a serious risk for the platform, so it is safer to do it with the `safeSetOwner` pattern


**Recommendation:**
Use ``Ownable2Step.sol``
[Ownable2Step.sol](https://github.com/OpenZeppelin/openzeppelin-contracts/blob/master/contracts/access/Ownable2Step.sol)

### [L-04] There is a risk that the `proxyFee` variable is accidentally initialized to 0 and platform loses money


### Impact

With the `setProtocolFeeRatio()` in the ` Factory.sol `  and `Pool.sol` files, the initial rate of ` protocolFeeRatio ` is set with an argument of type uint16, but there is no check that prevents this rate from starting with 0.
There is a risk that the `protocolFeeRatio` variable is accidentally initialized to 0

Starting proxyFee with 0 is an administrative decision, but since there is no information about this in the documentation and NatSpec comments during the audit, we can assume that it will not be 0

In addition, it is a strong belief that it will not be 0, as it is an issue that will affect the platform revenues.

Although the value initialized with 0 by mistake or forgetting can be changed later by onlyOwner/Owner, in the first place it can be exploited by users and cause huge amount  usage

`require == 0` should not be made because of the possibility of the platform defining the `0` rate.

With bool it can be confirmed that checking for 0 and this will not skip `function setProtocolFeeRatio(uint16 _protocolFeeRatio, bool isZero)`
In this way ; It can be set to `0` at any time and the possibility of error is eliminated.

```js
2 results - 2 files

maverick-v1/contracts/models/Factory.sol:
  32  
  33:     function setProtocolFeeRatio(uint16 _protocolFeeRatio) external {
  34:         require(msg.sender == owner, "Factory:NOT_ALLOWED");
  35:         require(_protocolFeeRatio <= ONE_3_DECIMAL_SCALE, "Factory:PROTOCOL_FEE_CANNOT_EXCEED_ONE");
  36:         protocolFeeRatio = _protocolFeeRatio;
  37:         emit SetFactoryProtocolFeeRatio(_protocolFeeRatio);
  38:     }

maverick-v1/contracts/models/Pool.sol:
  331  
  332:     function setProtocolFeeRatio(uint16 _protocolFeeRatio) internal {
  333:         require(_protocolFeeRatio <= ONE_3_DECIMAL_SCALE);
  334:         state.protocolFeeRatio = _protocolFeeRatio;
  335: 
  336:         emit SetProtocolFeeRatio(_protocolFeeRatio);
  337:     }

```

### [L-05] Use ```safeTransferOwnership``` instead of ```transferOwnership``` function

**Context:**

[Position.sol#L12](https://github.com/code-423n4/2022-12-Stealth-Project/blob/main/maverick-v1/contracts/models/Position.sol#L12)

[PositionMetadata.sol#L8](https://github.com/code-423n4/2022-12-Stealth-Project/blob/main/maverick-v1/contracts/models/PositionMetadata.sol#L8)

**Description:**
```transferOwnership``` function is used to change Ownership from ```Ownable.sol```.

Use a 2 structure transferOwnership which is safer. 
```safeTransferOwnership```,  use it is more secure due to 2-stage ownership transfer.

**Recommendation:**
Use ``Ownable2Step.sol``
[Ownable2Step.sol](https://github.com/OpenZeppelin/openzeppelin-contracts/blob/master/contracts/access/Ownable2Step.sol)

### [L-06] Owner can renounce Ownership

**Context:**
[Position.sol#L12](https://github.com/code-423n4/2022-12-Stealth-Project/blob/main/maverick-v1/contracts/models/Position.sol#L12)

[PositionMetadata.sol#L8](https://github.com/code-423n4/2022-12-Stealth-Project/blob/main/maverick-v1/contracts/models/PositionMetadata.sol#L8)


**Description:**
Typically, the contract’s owner is the account that deploys the contract. As a result, the owner is able to perform certain privileged activities.

The Openzeppelin’s Ownable used in this project contract implements renounceOwnership. This can represent a certain risk if the ownership is renounced for any other reason than by design. Renouncing ownership will leave the contract without an owner, thereby removing any functionality that is only available to the owner.

`onlyOwner` functions;
```js
maverick-v1/contracts/models/PositionMetadata.sol:
   8: contract PositionMetadata is IPositionMetadata, Ownable {

  17:     function setBaseURI(string memory _baseURI) external onlyOwner {
  21:     function tokenURI(uint256 tokenId) external view override returns (string memory) {


maverick-v1/contracts/models/Position.sol:
  12: contract Position is ERC721, ERC721Enumerable, Ownable, IPosition {

  23:     function setMetadata(IPositionMetadata _metadata) external onlyOwner {

```

**Recommendation:**
We recommend to either reimplement the function to disable it or to clearly specify if it is part of the contract design.

### [L-07] Missing Event for critical parameters change

**Context:**
```js
1 result - 1 file

maverick-v1/contracts/models/PositionMetadata.sol:
  16  
  17:     function setBaseURI(string memory _baseURI) external onlyOwner {
  18:         baseURI = _baseURI;
  19:     }

```

**Description:**
Events help non-contract tools to track changes, and events prevent users from being surprised by changes

**Recommendation:**
Add Event-Emit

### [L-08]  A single point of failure 

[Position.sol#L12](https://github.com/code-423n4/2022-12-Stealth-Project/blob/main/maverick-v1/contracts/models/Position.sol#L12)

[PositionMetadata.sol#L8](https://github.com/code-423n4/2022-12-Stealth-Project/blob/main/maverick-v1/contracts/models/PositionMetadata.sol#L8)


## Impact

The `owner` role has a single point of failure and `onlyOwner` can use critical a few functions.

`owner` role in the project:
Owner is not behind a multisig and changes are not behind a timelock.

Even if protocol admins/developers are not malicious there is still a chance for Owner keys to be stolen. In such a case, the attacker can cause serious damage to the project due to important functions. In such a case, users who have invested in project will suffer high financial losses.


`onlyOwner` functions;
```js
maverick-v1/contracts/models/PositionMetadata.sol:
   8: contract PositionMetadata is IPositionMetadata, Ownable {

  17:     function setBaseURI(string memory _baseURI) external onlyOwner {
  21:     function tokenURI(uint256 tokenId) external view override returns (string memory) {


maverick-v1/contracts/models/Position.sol:
  12: contract Position is ERC721, ERC721Enumerable, Ownable, IPosition {

  23:     function setMetadata(IPositionMetadata _metadata) external onlyOwner {

```

This increases the risk of ```A single point of failure```





## Tools Used
Manuel Code Review


## Recommended Mitigation Steps
Add a time lock to  critical functions. Admin-only functions that change critical parameters should emit events and have timelocks. 

Events allow capturing the changed parameters so that off-chain tools/interfaces can register such changes with timelocks that allow users to evaluate them and consider if they would like to engage/exit based on how they perceive the changes as affecting the trustworthiness of the protocol or profitability of the implemented financial services.

Allow only multi-signature wallets to call the function to reduce the likelihood of an attack.

https://twitter.com/danielvf/status/1572963475101556738?s=20&t=V1kvzfJlsx-D2hfnG0OmuQ

Also detail them in documentation and NatSpec comments

### [L-09] Some ERC20 tokens should need to approve(0) first

Some tokens (like USDT) do not work when changing the allowance from an existing non-zero allowance value. They must first be approved by zero and then the actual allowance must be approved.


```solidity

1 result - 1 file

router-v1/contracts/libraries/TransferHelper.sol:
  32      /// @param value The amount of the given token the target will be allowed to spend
  33:     function safeApprove(address token, address to, uint256 value) internal {
  34:         (bool success, bytes memory data) = token.call(abi.encodeWithSelector(IERC20.approve.selector, to, value));
  35:         require(success && (data.length == 0 || abi.decode(data, (bool))), "SA");
  36:     }

```

When using one of these unsupported tokens, all transactions revert and the protocol cannot be used.

Recommended Mitigation Steps
approve with a zero amount first before setting the actual amount.

```solidity
+          IERC20.approve.selector, to, 0);
            IERC20.approve.selector, to, value);
```

### [L-10] Not using the latest version of `prb-math` from dependencies

`prb-math` is an important mathematical library
The package.json configuration file says that the project is using 2.2.0 of `prb-math` which has a not last update version



```js
1 result - 1 file

router-v1/package.json:
  17:   "dependencies": {
  24:     "prb-math": "^2.2.0",
```

**Recommendation:**
Use patched versions

### [L-11] Loss of precision due to rounding


```solidity

router-v1/contracts/libraries/Path.sol:
  15:     uint256 private constant ADDR_SIZE = 20;
  18:     uint256 private constant NEXT_OFFSET = ADDR_SIZE + ADDR_SIZE;

  34:     function numPools(bytes memory path) internal pure returns (uint256) {
  35:         // Ignore the first token address. From then on every fee and token offset indicates a pool.
  36:         return ((path.length - ADDR_SIZE) / NEXT_OFFSET);
  37:     }

```
### [N-01] Critical Address Changes Should Use Two-step Procedure

The critical procedures should be two step process.


```solidity
2 results - 2 files

maverick-v1/contracts/models/Factory.sol:
  40:     function setOwner(address _owner) external {
  41          require(msg.sender == owner && _owner != address(0), "Factory:NOT_ALLOWED");

maverick-v1/contracts/models/Position.sol:
  22  
  23:     function setMetadata(IPositionMetadata _metadata) external onlyOwner {
  24          metadata = _metadata;

```

Recommended Mitigation Steps
Lack of two-step procedure for critical operations leaves them error-prone. Consider adding two step procedure on the critical functions.


### [N-02] Initial value check is missing in Set Functions

**Context:**
7 results 1 files

```solidity

5 results - 4 files

maverick-v1/contracts/models/Factory.sol:
  32  
  33:     function setProtocolFeeRatio(uint16 _protocolFeeRatio) external {
  34          require(msg.sender == owner, "Factory:NOT_ALLOWED");

  39  
  40:     function setOwner(address _owner) external {
  41          require(msg.sender == owner && _owner != address(0), "Factory:NOT_ALLOWED");

maverick-v1/contracts/models/Pool.sol:
  331  
  332:     function setProtocolFeeRatio(uint16 _protocolFeeRatio) internal {
  333          require(_protocolFeeRatio <= ONE_3_DECIMAL_SCALE);

maverick-v1/contracts/models/Position.sol:
  22  
  23:     function setMetadata(IPositionMetadata _metadata) external onlyOwner {
  24          metadata = _metadata;

maverick-v1/contracts/models/PositionMetadata.sol:
  16  
  17:     function setBaseURI(string memory _baseURI) external onlyOwner {
  18          baseURI = _baseURI;

```
Checking whether the current value and the new value are the same should be added
### [N-03] Critical dependencies such as OpenZeppelin should use the latest version

```js
maverick-v1/package.json:
  69    },
  70:   "dependencies": {
  71:     "@openzeppelin/contracts": ">=4.1.0 <4.8.0",

```

As shown above, it is safer to specify the latest version definitively, rather than intermittently specified version dependencies

### [N-04] Not using the named return variables anywhere in the function is confusing


Within the scope of the project, it is observed that this is not followed in general, the following codes can be given as an example

```solidity

4 results - 4 files

maverick-v1/contracts/libraries/Delta.sol:
  38:     function sqrtEdgePrice(Instance memory self) internal pure returns (uint256 edge) {
  39:         return (self.tokenAIn ? self.sqrtUpperTickPrice : self.sqrtLowerTickPrice);
  40:     }

maverick-v1/contracts/models/Pool.sol:
  315  
  316:     function balanceOf(uint256 tokenId, uint128 binId) external view override returns (uint256 lpToken) {
  317:         return bins[binId].balances[tokenId];

maverick-v1/contracts/models/PoolInspector.sol:
  64:     function calculateSwap(IPool pool, uint128 amount, bool tokenAIn, bool exactOutput) external returns (uint256 amountOut) {
  65:         try pool.swap(address(this), amount, tokenAIn, exactOutput, 0, abi.encode(exactOutput)) {} catch Error(string memory _data) {
  66:             if (bytes(_data).length == 0) {
  67:                 revert("Invalid Swap");
  68:             }
  69:             return abi.decode(bytes(_data), (uint256));
  70:         }

maverick-v1/contracts/test/TestPool.sol:
  43:     function swap(address recipient, uint128 amount, bool tokenAIn, bool exactOutput) external returns (uint256 amountIn, uint256 amountOut) {
  44:         SwapCallbackData memory data = SwapCallbackData({tokenIn: tokenAIn ? pool.tokenA() : pool.tokenB(), tokenOut: tokenAIn ? pool.tokenB() : pool.tokenA(), payer: msg.sender});
  45:         return pool.swap(recipient, amount, tokenAIn, exactOutput, 0, abi.encode(data));
  46:     }


```

**Recommendation:**
Consider adopting a consistent approach to return values throughout the codebase by removing all named return variables, explicitly declaring them as local variables, and adding the necessary return statements where appropriate. This would improve both the explicitness and readability of the code, and it may also help reduce regressions during future code refactors.
### [N-05] Use a single file for all system-wide constants

There are many addresses and constants used in the system. It is recommended to put the most used ones in one file (for example constants.sol, use inheritance to access these values)

  This will help with readability and easier maintenance for future changes. 

constants.sol
Use and import this file in contracts that require access to these values. This is just a suggestion, in some use cases this may result in higher gas usage in the distribution

```solidity
29 results - 5 files

maverick-v1/contracts/libraries/BinMap.sol:
   7: import "./Constants.sol";
  10:     uint8 constant TWO_BIT_MASK = 0xFC;
  11:     int32 constant NUMBER_OF_KINDS_32 = int32(uint32(NUMBER_OF_KINDS));
  12:     uint32 constant KINDS_BITMASK = 15;
  13:     uint16 constant WORD_SIZE = 256;

maverick-v1/contracts/libraries/Constants.sol:
   4: uint8 constant NUMBER_OF_KINDS = 4;
   5: uint256 constant MAX_TICK = 460540;
   6: uint256 constant MERGED_LP_BALANCE_TOKEN_ID = 0;
   8: uint8 constant KIND_STATIC = 1;
   9: uint8 constant KIND_RIGHT = 2;
  10: uint8 constant KIND_LEFT = 4;
  11: uint8 constant KIND_BOTH = 8;
  13: uint256 constant PROTOCOL_FEE_SCALE = 1e15;
  15: uint8 constant DEFAULT_DECIMALS = 18;
  16: uint256 constant DEFAULT_SCALE = 1;
  17: uint256 constant MAX_BIT = 0x8000000000000000000000000000000000000000000000000000000000000000;

maverick-v1/contracts/models/Factory.sol:
  20:     uint16 constant ONE_3_DECIMAL_SCALE = 1e3;

maverick-v1/contracts/models/Pool.sol:
  31:     uint8 constant NO_EMERGENCY_UNLOCKED = 0;
  32:     uint8 constant LOCKED = 1;
  33:     uint8 constant EMERGENCY = 2;
  35:     uint256 constant ACTION_EMERGENCY_MODE = 911;
  36:     uint256 constant ACTION_SET_PROTOCOL_FEES = 1;
  37:     uint256 constant ACTION_CLAIM_PROTOCOL_FEES_A = 2;
  38:     uint256 constant ACTION_CLAIM_PROTOCOL_FEES_B = 3;
  40:     uint16 constant ONE_3_DECIMAL_SCALE = 1e3;

router-v1/contracts/libraries/Path.sol:
  15:     uint256 private constant ADDR_SIZE = 20;
  18:     uint256 private constant NEXT_OFFSET = ADDR_SIZE + ADDR_SIZE;
  20:     uint256 private constant POP_OFFSET = NEXT_OFFSET + ADDR_SIZE;
  22:     uint256 private constant MULTIPLE_POOLS_MIN_LENGTH = POP_OFFSET + NEXT_OFFSET;


```
### [N-06] NatSpec comments should be increased in contracts

**Context:**
All Contracts

**Description:**
It is recommended that Solidity contracts are fully annotated using NatSpec for all public interfaces (everything in the ABI). It is clearly stated in the Solidity official documentation.
In complex projects such as Defi, the interpretation of all functions and their arguments and returns is important for code readability and auditability.
https://docs.soliditylang.org/en/v0.8.15/natspec-format.html

**Recommendation:**
NatSpec comments should be increased in contracts

### [N-07] For functions, follow Solidity standard naming conventions

**Context:**
All Contract `internal` functions

**Description:**
The above codes don't follow Solidity's standard naming convention,

internal and private functions : the mixedCase format starting with an underscore (_mixedCase starting with an underscore)


### [NC-08] `Function writing` that does not comply with the `Solidity Style Guide`

**Context:**
All Contracts

**Description:**
Order of Functions; ordering helps readers identify which functions they can call and to find the constructor and fallback definitions easier. But there are contracts in the project that do not comply with this.

https://docs.soliditylang.org/en/v0.8.17/style-guide.html

Functions should be grouped according to their visibility and ordered:

 constructor
receive function (if exists)
fallback function (if exists)
external
public
internal
private
within a grouping, place the view and pure functions last

### [N-09] Add a timelock to critical functions

It is a good practice to give time for users to react and adjust to critical changes. A timelock provides more guarantees and reduces the level of trust required, thus decreasing risk for users. It also indicates that the project is legitimate (less risk of a malicious owner making a sandwich attack on a user).
Consider adding a timelock to:

```solidity

5 results - 4 files

maverick-v1/contracts/models/Factory.sol:
  32  
  33:     function setProtocolFeeRatio(uint16 _protocolFeeRatio) external {
  34          require(msg.sender == owner, "Factory:NOT_ALLOWED");

  39  
  40:     function setOwner(address _owner) external {
  41          require(msg.sender == owner && _owner != address(0), "Factory:NOT_ALLOWED");

maverick-v1/contracts/models/Pool.sol:
  331  
  332:     function setProtocolFeeRatio(uint16 _protocolFeeRatio) internal {
  333          require(_protocolFeeRatio <= ONE_3_DECIMAL_SCALE);

maverick-v1/contracts/models/Position.sol:
  22  
  23:     function setMetadata(IPositionMetadata _metadata) external onlyOwner {
  24          metadata = _metadata;

maverick-v1/contracts/models/PositionMetadata.sol:
  16  
  17:     function setBaseURI(string memory _baseURI) external onlyOwner {
  18          baseURI = _baseURI;
```

### [N-10] Use a more recent version of Solidity

**Context:**
All contracts

**Description:**
For security, it is best practice to use the latest Solidity version.
For the security fix list in the versions;
https://github.com/ethereum/solidity/blob/develop/Changelog.md


**Recommendation:**
Old version of Solidity is used , newer version can be used `(0.8.17)` 
### [N-11] Solidity compiler optimizations can be problematic

```js

2 results - 2 files

maverick-v1/hardhat.config.ts:
  24            optimizer: {
  25:             enabled: true,
  26              runs: 80,

router-v1/hardhat.config.ts:
  25            optimizer: {
  26:             enabled: true,
  27              runs: 20
```

**Description:**
Protocol has enabled optional compiler optimizations in Solidity.
There have been several optimization bugs with security implications. Moreover, optimizations are actively being developed. Solidity compiler optimizations are disabled by default, and it is unclear how many contracts in the wild actually use them. 

Therefore, it is unclear how well they are being tested and exercised.
High-severity security issues due to optimization bugs have occurred in the past. A high-severity bug in the emscripten-generated solc-js compiler used by Truffle and Remix persisted until late 2018. The fix for this bug was not reported in the Solidity CHANGELOG. 

Another high-severity optimization bug resulting in incorrect bit shift results was patched in Solidity 0.5.6. More recently, another bug due to the incorrect caching of keccak256 was reported.
A compiler audit of Solidity from November 2018 concluded that the optional optimizations may not be safe.
It is likely that there are latent bugs related to optimization and that new bugs will be introduced due to future optimizations.

Exploit Scenario
A latent or future bug in Solidity compiler optimizations—or in the Emscripten transpilation to solc-js—causes a security vulnerability in the contracts.


**Recommendation:**
Short term, measure the gas savings from optimizations and carefully weigh them against the possibility of an optimization-related bug.
Long term, monitor the development and adoption of Solidity compiler optimizations to assess their maturity.

### [N-12] Insufficient coverage

**Description:**
The test coverage rate of the project is 81%. Testing all functions is best practice in terms of security criteria.

```js

router-v1

--------------------------------|----------|----------|----------|----------|----------------|
File                            |  % Stmts | % Branch |  % Funcs |  % Lines |Uncovered Lines |
--------------------------------|----------|----------|----------|----------|----------------|
 contracts/                     |    98.91 |    72.73 |      100 |    98.89 |                |
  Router.sol                    |    98.91 |    72.73 |      100 |    98.89 |            264 |
 contracts/interfaces/          |      100 |      100 |      100 |      100 |                |
  IMulticall.sol                |      100 |      100 |      100 |      100 |                |
  IRouter.sol                   |      100 |      100 |      100 |      100 |                |
  ISelfPermit.sol               |      100 |      100 |      100 |      100 |                |
 contracts/interfaces/external/ |      100 |      100 |      100 |      100 |                |
  IERC20PermitAllowed.sol       |      100 |      100 |      100 |      100 |                |
  IWETH9.sol                    |      100 |      100 |      100 |      100 |                |
 contracts/libraries/           |    62.79 |    34.38 |    61.11 |    66.67 |                |
  BytesLib.sol                  |    69.23 |    35.71 |    66.67 |    68.75 | 87,88,89,91,95 |
  Deadline.sol                  |      100 |      100 |      100 |      100 |                |
  Multicall.sol                 |     62.5 |       25 |      100 |     62.5 |       18,19,22 |
  Path.sol                      |    85.71 |      100 |       80 |    85.71 |             36 |
  SelfPermit.sol                |        0 |        0 |        0 |        0 |    17,22,27,32 |
  TransferHelper.sol            |       75 |     37.5 |       75 |       75 |          34,35 |
 contracts/test/                |      100 |      100 |      100 |      100 |                |
  ImportExternal.sol            |      100 |      100 |      100 |      100 |                |
--------------------------------|----------|----------|----------|----------|----------------|
All files                       |    87.41 |     60.2 |    81.58 |    88.15 |                |
--------------------------------|----------|----------|----------|----------|----------------|

```
Due to its capacity, test coverage is expected to be 100%.

### [N-13] For modern and more readable code; update import usages

**Context:**

```solidity

128 results - 32 files

```


**Description:**
Solidity code is also cleaner in another way that might not be noticeable: the struct Point. We were importing it previously with global import but not using it. The Point struct `polluted the source code` with an unnecessary object we were not using because we did not need it. 
This was breaking the rule of modularity and modular programming: `only import what you need` Specific imports with curly braces allow us to apply this rule better.

**Recommendation:**
`import {contract1 , contract2} from "filename.sol";`

A good example from the ArtGobblers project;
```js
import {Owned} from "solmate/auth/Owned.sol";
import {ERC721} from "solmate/tokens/ERC721.sol";
import {LibString} from "solmate/utils/LibString.sol";
import {MerkleProofLib} from "solmate/utils/MerkleProofLib.sol";
import {FixedPointMathLib} from "solmate/utils/FixedPointMathLib.sol";
import {ERC1155, ERC1155TokenReceiver} from "solmate/tokens/ERC1155.sol";
import {toWadUnsafe, toDaysWadUnsafe} from "solmate/utils/SignedWadMath.sol";
```
### [N-14] `WETH` address definition can be use _directly_

**Context:**
```solidity
1 result - 1 file

router-v1/contracts/Router.sol:
  45      /// @inheritdoc IRouter
  46:     IWETH9 public immutable override WETH9;
```

**Description:**
WETH is a wrap Ether contract with a specific address in the Etherum network, giving the option to define it may cause false recognition, it is healthier to define it directly.

Advantages of defining a specific contract directly:
- It saves gas,
- Prevents incorrect argument definition, 
- Prevents execution on a different chain and re-signature issues,

WETH Address : 0xc02aaa39b223fe8d0a0e5c4f27ead9083c756cc2


**Recommendation:**
```js
address private constant WETH = 0xc02aaa39b223fe8d0a0e5c4f27ead9083c756cc2;
```
### [N-15] Floating pragma

**Description:**
Contracts should be deployed with the same compiler version and flags that they have been tested with thoroughly. Locking the pragma helps to ensure that contracts do not accidentally get deployed using, for example, an outdated compiler version that might introduce bugs that affect the contract system negatively.
https://swcregistry.io/docs/SWC-103

Floating Pragma List: 
pragma ^0.8.0   (all contracts)

**Recommendation:**
Lock the pragma version and also consider known bugs (https://github.com/ethereum/solidity/releases) for the compiler version that is chosen.

### [N-16] Add to indexed parameter for countable Events

```solidity
maverick-v1/contracts/interfaces/IFactory.sol:
  9:     event PoolCreated(address poolAddress, uint256 fee, uint256 tickSpacing, int32 activeTick, uint32 lookback, uint16 protocolFeeRatio, IERC20 tokenA, IERC20 tokenB);

```

Add to indexed parameter for countable Events


### [N-17] Include return parameters in NatSpec comments

**Context:**
All Contracts

**Description:**
It is recommended that Solidity contracts are fully annotated using NatSpec for all public interfaces (everything in the ABI). It is clearly stated in the Solidity official documentation. In complex projects such as Defi, the interpretation of all functions and their arguments and returns is important for code readability and auditability.

https://docs.soliditylang.org/en/v0.8.15/natspec-format.html

**Recommendation:**
Include return parameters in NatSpec comments

_Recommendation  Code Style: (from Uniswap3)_
```js
    /// @notice Adds liquidity for the given recipient/tickLower/tickUpper position
    /// @dev The caller of this method receives a callback in the form of IUniswapV3MintCallback#uniswapV3MintCallback
    /// in which they must pay any token0 or token1 owed for the liquidity. The amount of token0/token1 due depends
    /// on tickLower, tickUpper, the amount of liquidity, and the current price.
    /// @param recipient The address for which the liquidity will be created
    /// @param tickLower The lower tick of the position in which to add liquidity
    /// @param tickUpper The upper tick of the position in which to add liquidity
    /// @param amount The amount of liquidity to mint
    /// @param data Any data that should be passed through to the callback
    /// @return amount0 The amount of token0 that was paid to mint the given amount of liquidity. Matches the value in the callback
    /// @return amount1 The amount of token1 that was paid to mint the given amount of liquidity. Matches the value in the callback
    function mint(
        address recipient,
        int24 tickLower,
        int24 tickUpper,
        uint128 amount,
        bytes calldata data
    ) external returns (uint256 amount0, uint256 amount1);
```

### [N-18] Omissions in Events

Throughout the codebase, events are generally emitted when sensitive changes are made to the contracts. However, some events are missing important parameters

The events should include the new value and old value where possible:

```solidity

4 results  4 files

src/minters/FixedPriceFactory.sol:
  48:     function setFeeReceiver(address payable fees) public onlyOwner {
  49          feeReceiver = fees;

src/minters/LPDAFactory.sol:
  45      /// @param fees the address to receive fees
  46:     function setFeeReceiver(address payable fees) public onlyOwner {
  47          feeReceiver = fees;

src/minters/OpenEditionFactory.sol:
  41      /// @param fees the address to receive fees
  42:     function setFeeReceiver(address payable fees) public onlyOwner {
  43          feeReceiver = fees;

maverick-v1/contracts/models/Position.sol:
  22  
  23:     function setMetadata(IPositionMetadata _metadata) external onlyOwner {
  24:         metadata = _metadata;
  25:         emit SetMetadata(metadata);
  26:     }

```

## [N-19] Long lines are not suitable for the ‘Solidity Style Guide’

**Context:**
[Bin.sol#L37](https://github.com/code-423n4/2022-12-Stealth-Project/blob/main/maverick-v1/contracts/libraries/Bin.sol#L37)
[Bin.sol#L40](https://github.com/code-423n4/2022-12-Stealth-Project/blob/main/maverick-v1/contracts/libraries/Bin.sol#L40)
[Bin.sol#L56](https://github.com/code-423n4/2022-12-Stealth-Project/blob/main/maverick-v1/contracts/libraries/Bin.sol#L56)
[Bin.sol#L84](https://github.com/code-423n4/2022-12-Stealth-Project/blob/main/maverick-v1/contracts/libraries/Bin.sol#L84)
[Bin.sol#L121](https://github.com/code-423n4/2022-12-Stealth-Project/blob/main/maverick-v1/contracts/libraries/Bin.sol#L121)
[Bin.sol#L146](https://github.com/code-423n4/2022-12-Stealth-Project/blob/main/maverick-v1/contracts/libraries/Bin.sol#L146)
[BinMap.sol#L41](https://github.com/code-423n4/2022-12-Stealth-Project/blob/main/maverick-v1/contracts/libraries/BinMap.sol#L41)
[BinMap.sol#L75](https://github.com/code-423n4/2022-12-Stealth-Project/blob/main/maverick-v1/contracts/libraries/BinMap.sol#L75)
[BinMap.sol#L41](https://github.com/code-423n4/2022-12-Stealth-Project/blob/main/maverick-v1/contracts/libraries/BinMap.sol#L41)
[BinMath.sol#L121](https://github.com/code-423n4/2022-12-Stealth-Project/blob/main/maverick-v1/contracts/libraries/BinMath.sol#L121)
[BinMath.sol#L134](https://github.com/code-423n4/2022-12-Stealth-Project/blob/main/maverick-v1/contracts/libraries/BinMath.sol#L134)
[Delta.sol#L35](https://github.com/code-423n4/2022-12-Stealth-Project/blob/main/maverick-v1/contracts/libraries/Delta.sol#L35)
[Twa.sol#L32](https://github.com/code-423n4/2022-12-Stealth-Project/blob/main/maverick-v1/contracts/libraries/Twa.sol#L32)
[Factory.sol#L46](https://github.com/code-423n4/2022-12-Stealth-Project/blob/main/maverick-v1/contracts/models/Factory.sol#L46)
[Factory.sol#L58](https://github.com/code-423n4/2022-12-Stealth-Project/blob/main/maverick-v1/contracts/models/Factory.sol#L58)
[Pool.sol#L238](https://github.com/code-423n4/2022-12-Stealth-Project/blob/main/maverick-v1/contracts/models/Pool.sol#L238)
[Pool.sol#L270](https://github.com/code-423n4/2022-12-Stealth-Project/blob/main/maverick-v1/contracts/models/Pool.sol#L270)
[Pool.sol#L486](https://github.com/code-423n4/2022-12-Stealth-Project/blob/main/maverick-v1/contracts/models/Pool.sol#L486)
[Pool.sol#L516](https://github.com/code-423n4/2022-12-Stealth-Project/blob/main/maverick-v1/contracts/models/Pool.sol#L516)
[Pool.sol#L589](https://github.com/code-423n4/2022-12-Stealth-Project/blob/main/maverick-v1/contracts/models/Pool.sol#L589)
[Pool.sol#L639](https://github.com/code-423n4/2022-12-Stealth-Project/blob/main/maverick-v1/contracts/models/Pool.sol#L639)
[PoolInspector.sol#L107](https://github.com/code-423n4/2022-12-Stealth-Project/blob/main/maverick-v1/contracts/models/PoolInspector.sol#L107)
[SelfPermit.sol#L31](https://github.com/code-423n4/2022-12-Stealth-Project/blob/main/router-v1/contracts/libraries/SelfPermit.sol#L31)
[Router.sol#L121](https://github.com/code-423n4/2022-12-Stealth-Project/blob/main/router-v1/contracts/Router.sol#L121)
[Router.sol#L181](https://github.com/code-423n4/2022-12-Stealth-Project/blob/main/router-v1/contracts/Router.sol#L181)
[Router.sol#L272](https://github.com/code-423n4/2022-12-Stealth-Project/blob/main/router-v1/contracts/Router.sol#L272)

**Description:**
It is generally recommended that lines in the source code should not exceed 80-120 characters. Today's screens are much larger, so in some cases it makes sense to expand that. The lines above should be split when they reach that length, as the files will most likely be on GitHub and GitHub always uses a scrollbar when the length is more than 164 characters.

(why-is-80-characters-the-standard-limit-for-code-width)[https://softwareengineering.stackexchange.com/questions/148677/why-is-80-characters-the-standard-limit-for-code-width]

**Recommendation:**
Multiline output parameters and return statements should follow the same style recommended for wrapping long lines found in the Maximum Line Length section.

https://docs.soliditylang.org/en/v0.8.17/style-guide.html#introduction

```js
thisFunctionCallIsReallyLong(
    longArgument1,
    longArgument2,
    longArgument3
);
```

### [N-20] `abicoder v2` is enabled by default

```solidity
router-v1/contracts/libraries/Multicall.sol:
  2  pragma solidity ^0.8.0;
  3: pragma abicoder v2;

```


abicoder v2 is considered non-experimental as of Solidity 0.6.0 and it is enabled by default starting with Solidity 0.8.0. Therefore, there is no need to write

[abi-coder-pragma](https://docs.soliditylang.org/en/v0.8.17/layout-of-source-files.html#abi-coder-pragma)

### [N-21] Need Fuzzing test

```solidity
2 result - 1 file

maverick-v1/contracts/libraries/BinMath.sol:
  11  
  12:     function msb(uint256 x) internal pure returns (uint8 result) {
  13:         unchecked {
  14:             result = 0;
  15: 
  16:             if (x >= 0x100000000000000000000000000000000) {
  17:                 x >>= 128;
  18:                 result += 128;
  19:             }
  20:             if (x >= 0x10000000000000000) {
  21:                 x >>= 64;
  22:                 result += 64;
  23:             }
  24:             if (x >= 0x100000000) {
  25:                 x >>= 32;
  26:                 result += 32;
  27:             }
  28:             if (x >= 0x10000) {
  29:                 x >>= 16;
  30:                 result += 16;
  31:             }
  32:             if (x >= 0x100) {
  33:                 x >>= 8;
  34:                 result += 8;
  35:             }
  36:             if (x >= 0x10) {
  37:                 x >>= 4;
  38:                 result += 4;
  39:             }
  40:             if (x >= 0x4) {
  41:                 x >>= 2;
  42:                 result += 2;
  43:             }
  44:             if (x >= 0x2) result += 1;
  45:         }
  46:     }
  47: 
  48:     function lsb(uint256 x) internal pure returns (uint8 result) {
  49:         unchecked {
  50:             result = 255;
  51: 
  52:             if (x & 0xFFFFFFFFFFFFFFFFFFFFFFFFFFFFFFFF > 0) {
  53:                 result -= 128;
  54:             } else {
  55:                 x >>= 128;
  56:             }
  57:             if (x & 0xFFFFFFFFFFFFFFFF > 0) {
  58:                 result -= 64;
  59:             } else {
  60:                 x >>= 64;
  61:             }
  62:             if (x & 0xFFFFFFFF > 0) {
  63:                 result -= 32;
  64:             } else {
  65:                 x >>= 32;
  66:             }
  67:             if (x & 0xFFFF > 0) {
  68:                 result -= 16;
  69:             } else {
  70:                 x >>= 16;
  71:             }
  72:             if (x & 0xFF > 0) {
  73:                 result -= 8;
  74:             } else {
  75:                 x >>= 8;
  76:             }
  77:             if (x & 0xF > 0) {
  78:                 result -= 4;
  79:             } else {
  80:                 x >>= 4;
  81:             }
  82:             if (x & 0x3 > 0) {
  83:                 result -= 2;
  84:             } else {
  85:                 x >>= 2;
  86:             }
  87:             if (x & 0x1 > 0) result -= 1;
  88:         }
  89:     }
  90 
```

**Description:**
In total 1 contracts, 2 unchecked are used, the functions used are critical. For this reason, there must be fuzzing tests in the tests of the project. Not seen in tests.

**Recommendation:**
Use should fuzzing test like Echidna.

As Alberto Cuesta Canada said:
_Fuzzing is not easy, the tools are rough, and the math is hard, but it is worth it. Fuzzing gives me a level of confidence in my smart contracts that I didn’t have before. Relying just on unit testing anymore and poking around in a testnet seems reckless now._

https://medium.com/coinmonks/smart-contract-fuzzing-d9b88e0b0a05

### [N-22] Take advantage of Custom Error's return value property

An important feature of Custom Error is that values such as address, tokenID, msg.value can 
be written inside the `()` sign, this kind of approach provides a serious advantage in 
debugging and examining the revert details of dapps such as tenderly.

For Example;

```diff
1 result - 1 file

router-v1/contracts/libraries/Multicall.sol:
- 18:                if (result.length < 68) revert();
+ 18:                if (result.length < 68) revert(result.length);
```

### [N-23] Some `require` descriptions are not clear

Some `require` descriptions are not clear

Some `require` error descriptions below return results like A, B , C
1- It does not comply with the general `require` error description model of the project (Either all of them should be debugged in this way, or all of them should be explained with a string not exceeding 32 bytes.)

2- For debug dapps like Tenderly, these debug messages are important, this allows the user to see the reasons for revert practically


```solidity

5 results - 4 files

maverick-v1/contracts/libraries/Bin.sol:
   85:        require(deltaLpToken != 0, "L");
  140:        require(activeBin.state.mergeId == 0, "N");

maverick-v1/contracts/libraries/BinMath.sol:
  94:         require(tick <= MAX_TICK, "X");

maverick-v1/contracts/libraries/Cast.sol:
  6:         require((y = uint128(x)) == x, "C");

maverick-v1/contracts/libraries/SafeERC20Min.sol:
  18:        require(abi.decode(returndata, (bool)), "T");

```

### [S-01] Make the Test Context with Solidity

It's crucial to write tests with possibly 100% coverage for smart contract systems.

It is recommended to write appropriate tests for all possible code streams and especially for extreme cases.

But the other important point is the test context, tests written with solidity are safer, it is recommended to focus on tests with Foundry

### [S-02] Generate perfect code headers every time

**Description:**
I recommend using header for Solidity code layout and readability

https://github.com/transmissions11/headers

```js
/*//////////////////////////////////////////////////////////////
                           TESTING 123
//////////////////////////////////////////////////////////////*/
```
