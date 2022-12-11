

## Summary

### Gas Optimizations
| |Issue|Instances|Total Gas Saved|
|-|:-|:-:|:-:|
| [G&#x2011;01] | State variables only set in the constructor should be declared `immutable` | 2 |  2097 |
| [G&#x2011;02] | Structs can be packed into fewer storage slots | 2 |  - |
| [G&#x2011;03] | Using `storage` instead of `memory` for structs/arrays saves gas | 1 |  4200 |
| [G&#x2011;04] | Avoid contract existence checks by using low level calls | 11 |  1100 |
| [G&#x2011;05] | The result of function calls should be cached rather than re-calling the function | 4 |  - |
| [G&#x2011;06] | `<x> += <y>` costs more gas than `<x> = <x> + <y>` for state variables | 4 |  452 |
| [G&#x2011;07] | `internal` functions only called once can be inlined to save gas | 11 |  220 |
| [G&#x2011;08] | Add `unchecked {}` for subtractions where the operands cannot underflow because of a previous `require()` or `if`-statement | 1 |  85 |
| [G&#x2011;09] | `++i`/`i++` should be `unchecked{++i}`/`unchecked{i++}` when it is not possible for them to overflow, as is the case when used in `for`- and `while`-loops | 14 |  840 |
| [G&#x2011;10] | `require()`/`revert()` strings longer than 32 bytes cost extra gas | 4 |  - |
| [G&#x2011;11] | Optimize names to save gas | 17 |  374 |
| [G&#x2011;12] | Use a more recent version of solidity | 18 |  - |
| [G&#x2011;13] | `>=` costs less gas than `>` | 2 |  6 |
| [G&#x2011;14] | `++i` costs less gas than `i++`, especially when it's used in `for`-loops (`--i`/`i--` too) | 1 |  5 |
| [G&#x2011;15] | Splitting `require()` statements that use `&&` saves gas | 12 |  36 |
| [G&#x2011;16] | Usage of `uints`/`ints` smaller than 32 bytes (256 bits) incurs overhead | 50 |  - |
| [G&#x2011;17] | Inverting the condition of an `if`-`else`-statement wastes gas | 1 |  - |
| [G&#x2011;18] | `require()` or `revert()` statements that check input arguments should be at the top of the function | 2 |  - |
| [G&#x2011;19] | Functions guaranteed to revert when called by normal users can be marked `payable` | 2 |  42 |

Total: 159 instances over 19 issues with **9460 gas** saved

Gas totals use lower bounds of ranges and count two iterations of each `for`-loop. All values above are runtime, not deployment, values; deployment values are listed in the individual issue descriptions. The table above as well as its gas numbers do not include any of the excluded findings.





## Gas Optimizations

### [G&#x2011;01]  State variables only set in the constructor should be declared `immutable`
Avoids a Gsset (**20000 gas**) in the constructor, and replaces the first access in each transaction (Gcoldsload - **2100 gas**) and each access thereafter (Gwarmacces - **100 gas**) with a `PUSH32` (**3 gas**). 

In the example below, `twa.lookback` never changes, so it should be pulled out of the struct and converted to an immutable value. If you still want the struct to contain the lookback, create a private version of the twa struct, for storage purposes, that does _not_ have the `lookback`, and use that to build the original twa structs on the fly, when requested, and pass the value as an argument to `TWA.getTwa()` when it's called. Since this is in the hot path, you'll save a lot of gas.


*There are 2 instances of this issue:*

```solidity
File: maverick-v1/contracts/models/Pool.sol

/// @audit twa.lookback (constructor)
77:           twa.lookback = _lookback;

/// @audit twa (access)
102:          return twa;

```
https://github.com/code-423n4/2022-12-Stealth-Project/blob/fc8589d7d8c1d8488fd97ccc46e1ff11c8426ac2/maverick-v1/contracts/models/Pool.sol#L77

### [G&#x2011;02]  Structs can be packed into fewer storage slots
Each slot saved can avoid an extra Gsset (**20000 gas**) for the first setting of the struct. Subsequent reads as well as writes have smaller gas savings

*There are 2 instances of this issue:*

```solidity
File: maverick-v1/contracts/libraries/Delta.sol

/// @audit Variable ordering with 10 slots instead of the current 11:
///           uint256(32):deltaInBinInternal, uint256(32):deltaInErc, uint256(32):deltaOutErc, uint256(32):excess, uint256(32):endSqrtPrice, uint256(32):sqrtPriceLimit, uint256(32):sqrtLowerTickPrice, uint256(32):sqrtUpperTickPrice, uint256(32):sqrtPrice, bool(1):tokenAIn, bool(1):exactOutput, bool(1):swappedToMaxPrice, bool(1):skipCombine, bool(1):decrementTick
5         struct Instance {
6             uint256 deltaInBinInternal;
7             uint256 deltaInErc;
8             uint256 deltaOutErc;
9             uint256 excess;
10            bool tokenAIn;
11            uint256 endSqrtPrice;
12            bool exactOutput;
13            bool swappedToMaxPrice;
14            bool skipCombine;
15            bool decrementTick;
16            uint256 sqrtPriceLimit;
17            uint256 sqrtLowerTickPrice;
18            uint256 sqrtUpperTickPrice;
19            uint256 sqrtPrice;
20:       }

```
https://github.com/code-423n4/2022-12-Stealth-Project/blob/fc8589d7d8c1d8488fd97ccc46e1ff11c8426ac2/maverick-v1/contracts/libraries/Delta.sol#L5-L20

```solidity
File: maverick-v1/contracts/models/Pool.sol

/// @audit Variable ordering with 8 slots instead of the current 10:
///           uint8[2](32):kinds, user-defined[](32):activeList, uint256(32):mergeBinBalance, uint256(32):binCounter, uint256(32):mergeBinCounter, uint128[](32):mergeBins, uint128(16):firstBinId, uint128(16):totalReserveA, uint128(16):totalReserveB, int32(4):tickLimit, int32(4):shiftAmount
363       struct MoveData {
364           int32 tickLimit;
365           uint8[2] kinds;
366           int32 shiftAmount;
367           BinMap.Active[] activeList;
368           uint128 firstBinId;
369           uint256 mergeBinBalance;
370           uint128 totalReserveA;
371           uint128 totalReserveB;
372           uint256 binCounter;
373           uint256 mergeBinCounter;
374           uint128[] mergeBins;
375:      }

```
https://github.com/code-423n4/2022-12-Stealth-Project/blob/fc8589d7d8c1d8488fd97ccc46e1ff11c8426ac2/maverick-v1/contracts/models/Pool.sol#L363-L375

### [G&#x2011;03]  Using `storage` instead of `memory` for structs/arrays saves gas
When fetching data from a storage location, assigning the data to a `memory` variable causes all fields of the struct/array to be read from storage, which incurs a Gcoldsload (**2100 gas**) for *each* field of the struct/array. If the fields are read from the new memory variable, they incur an additional `MLOAD` rather than a cheap stack read. Instead of declearing the variable with the `memory` keyword, declaring the variable with the `storage` keyword and caching any fields that need to be re-read in stack variables, will be much cheaper, only incuring the Gcoldsload for the fields actually read. The only time it makes sense to read the whole struct/array into a `memory` variable, is if the full struct/array is being returned by the function, is being passed to a function that requires `memory`, or if the array/struct is being read from another `memory` array/struct

*There is 1 instance of this issue:*

```solidity
File: maverick-v1/contracts/models/PoolInspector.sol

102:              IPool.BinState memory bin = bins[i];

```
https://github.com/code-423n4/2022-12-Stealth-Project/blob/fc8589d7d8c1d8488fd97ccc46e1ff11c8426ac2/maverick-v1/contracts/models/PoolInspector.sol#L102

### [G&#x2011;04]  Avoid contract existence checks by using low level calls
Prior to 0.8.10 the compiler inserted extra code, including `EXTCODESIZE` (**100 gas**), to check for contract existence for external function calls. In more recent solidity versions, the compiler will not insert these checks if the external call has a return value. Similar behavior can be achieved in earlier versions by using low-level calls, since low level calls never check for contract existence

*There are 11 instances of this issue:*

```solidity
File: maverick-v1/contracts/models/Factory.sol

/// @audit decimals()
74:               Math.scale(IERC20Metadata(address(_tokenA)).decimals()),

/// @audit decimals()
75:               Math.scale(IERC20Metadata(address(_tokenB)).decimals()),

```
https://github.com/code-423n4/2022-12-Stealth-Project/blob/fc8589d7d8c1d8488fd97ccc46e1ff11c8426ac2/maverick-v1/contracts/models/Factory.sol#L74

```solidity
File: maverick-v1/contracts/models/Pool.sol

/// @audit balanceOf()
502:          return IERC20(tokenA).balanceOf(address(this));

/// @audit balanceOf()
506:          return IERC20(tokenB).balanceOf(address(this));

```
https://github.com/code-423n4/2022-12-Stealth-Project/blob/fc8589d7d8c1d8488fd97ccc46e1ff11c8426ac2/maverick-v1/contracts/models/Pool.sol#L502

```solidity
File: router-v1/contracts/libraries/SelfPermit.sol

/// @audit allowance()
22:           if (IERC20(token).allowance(msg.sender, address(this)) < value) selfPermit(token, value, deadline, v, r, s);

/// @audit allowance()
32:           if (IERC20(token).allowance(msg.sender, address(this)) < type(uint256).max) selfPermitAllowed(token, nonce, expiry, v, r, s);

```
https://github.com/code-423n4/2022-12-Stealth-Project/blob/fc8589d7d8c1d8488fd97ccc46e1ff11c8426ac2/router-v1/contracts/libraries/SelfPermit.sol#L22

```solidity
File: router-v1/contracts/Router.sol

/// @audit tokenOfOwnerByIndexExists()
223:              if (IPosition(position).tokenOfOwnerByIndexExists(msg.sender, 0)) {

/// @audit tokenOfOwnerByIndex()
224:                  tokenId = IPosition(position).tokenOfOwnerByIndex(msg.sender, 0);

/// @audit mint()
226:                  tokenId = IPosition(position).mint(msg.sender);

/// @audit lookup()
269:              pool = IFactory(factory).lookup(poolParams.fee, poolParams.tickSpacing, poolParams.lookback, poolParams.tokenA, poolParams.tokenB);

/// @audit create()
272:              pool = IFactory(factory).create(poolParams.fee, poolParams.tickSpacing, poolParams.lookback, poolParams.activeTick, poolParams.tokenA, poolParams.tokenB);

```
https://github.com/code-423n4/2022-12-Stealth-Project/blob/fc8589d7d8c1d8488fd97ccc46e1ff11c8426ac2/router-v1/contracts/Router.sol#L223

### [G&#x2011;05]  The result of function calls should be cached rather than re-calling the function
The instances below point to the second+ call of the function within a single function

*There are 4 instances of this issue:*

```solidity
File: maverick-v1/contracts/models/Pool.sol

/// @audit sqrtPrice.inv() on line 589
589:              Math.mulDiv(binAmountIn, tokenAIn ? sqrtPrice.inv() : sqrtPrice, binAmountIn.div(liquidity) + (tokenAIn ? sqrtPrice : sqrtPrice.inv()), false)

/// @audit sqrtPrice.inv() on line 589
591:          delta.endSqrtPrice = binAmountIn.div(liquidity) + (tokenAIn ? sqrtPrice : sqrtPrice.inv());

/// @audit sqrtPrice.inv() on line 602
602:          binAmountIn = Math.mulDiv(delta.deltaOutErc, tokenAIn ? sqrtPrice : sqrtPrice.inv(), (tokenAIn ? sqrtPrice.inv() : sqrtPrice) - delta.deltaOutErc.div(liquidity), true);

/// @audit sqrtPrice.inv() on line 602
603:          delta.endSqrtPrice = (tokenAIn ? sqrtPrice.inv() : sqrtPrice) - delta.deltaOutErc.div(liquidity);

```
https://github.com/code-423n4/2022-12-Stealth-Project/blob/fc8589d7d8c1d8488fd97ccc46e1ff11c8426ac2/maverick-v1/contracts/models/Pool.sol#L589

### [G&#x2011;06]  `<x> += <y>` costs more gas than `<x> = <x> + <y>` for state variables
Using the addition operator instead of plus-equals saves **[113 gas](https://gist.github.com/IllIllI000/cbbfb267425b898e5be734d4008d4fe8)**

*There are 4 instances of this issue:*

```solidity
File: maverick-v1/contracts/models/Pool.sol

161:          binBalanceA += tokenAAmount.toUint128();

162:          binBalanceB += tokenBAmount.toUint128();

288:                  binBalanceA += delta.deltaInBinInternal.toUint128();

291:                  binBalanceB += delta.deltaInBinInternal.toUint128();

```
https://github.com/code-423n4/2022-12-Stealth-Project/blob/fc8589d7d8c1d8488fd97ccc46e1ff11c8426ac2/maverick-v1/contracts/models/Pool.sol#L161

### [G&#x2011;07]  `internal` functions only called once can be inlined to save gas
Not inlining costs **20 to 40 gas** because of two extra `JUMP` instructions and additional stack operations needed for function calls.

*There are 11 instances of this issue:*

```solidity
File: maverick-v1/contracts/models/PoolInspector.sol

77:       function _getBinsAtTick(IPool pool, int32 tick) internal view returns (IPool.BinState[] memory bins) {

95:       function _currentTickLiquidity(IPool pool) internal view returns (uint256 sqrtPrice, uint256 liquidity, uint256 reserveA, uint256 reserveB) {

```
https://github.com/code-423n4/2022-12-Stealth-Project/blob/fc8589d7d8c1d8488fd97ccc46e1ff11c8426ac2/maverick-v1/contracts/models/PoolInspector.sol#L77

```solidity
File: maverick-v1/contracts/models/Pool.sol

320:      function claimProtocolFees(address recipient, bool isTokenA) internal {

332:      function setProtocolFeeRatio(uint16 _protocolFeeRatio) internal {

448:      function _moveBins(int32 activeTick, int32 startingTickInitial, int32 lastTwap) internal {

486:      function _getOrCreateBin(State memory currentState, uint8 kind, int32 pos, bool isDelta) internal returns (uint128 binId, Bin.Instance storage bin) {

516:      function _currentTickLiquidity(int32 tick) internal view returns (uint256 sqrtLowerTickPrice, uint256 sqrtUpperTickPrice, uint256 sqrtPrice, uint128[] memory binIds, SwapData memory output) {

537:      function _firstKindAtTickLiquidity(int32 activeTick) internal view returns (uint256 currentReserveA, uint256 currentReserveB) {

553       function _computeSwapExactIn(
554           uint256 sqrtEdgePrice,
555           uint256 sqrtPrice,
556           uint256 liquidity,
557           uint256 reserveA,
558           uint256 reserveB,
559           uint256 amountIn,
560           bool limitInBin,
561           bool tokenAIn
562:      ) internal view returns (Delta.Instance memory delta) {

597:      function _computeSwapExactOut(uint256 sqrtPrice, uint256 liquidity, uint256 reserveA, uint256 reserveB, uint256 amountOut, bool tokenAIn) internal view returns (Delta.Instance memory delta) {

614:      function _swapTick(State memory currentState, Delta.Instance memory delta) internal returns (Delta.Instance memory newDelta) {

```
https://github.com/code-423n4/2022-12-Stealth-Project/blob/fc8589d7d8c1d8488fd97ccc46e1ff11c8426ac2/maverick-v1/contracts/models/Pool.sol#L320

### [G&#x2011;08]  Add `unchecked {}` for subtractions where the operands cannot underflow because of a previous `require()` or `if`-statement
`require(a <= b); x = b - a` => `require(a <= b); unchecked { x = b - a }`

*There is 1 instance of this issue:*

```solidity
File: maverick-v1/contracts/libraries/Math.sol

/// @audit if-condition on line 41
42:               return (10 ** (decimals - DEFAULT_DECIMALS)) | MAX_BIT;

```
https://github.com/code-423n4/2022-12-Stealth-Project/blob/fc8589d7d8c1d8488fd97ccc46e1ff11c8426ac2/maverick-v1/contracts/libraries/Math.sol#L42

### [G&#x2011;09]  `++i`/`i++` should be `unchecked{++i}`/`unchecked{i++}` when it is not possible for them to overflow, as is the case when used in `for`- and `while`-loops
The `unchecked` keyword is new in solidity version 0.8.0, so this only applies to that version or higher, which these instances are. This saves **30-40 gas [per loop](https://gist.github.com/hrkrshnn/ee8fabd532058307229d65dcd5836ddc#the-increment-in-for-loop-post-condition-can-be-made-unchecked)**

*There are 14 instances of this issue:*

```solidity
File: maverick-v1/contracts/models/PoolInspector.sol

30:           for (uint128 i = startBinIndex; i < binCounter; i++) {

80:           for (uint8 i = 0; i < NUMBER_OF_KINDS; i++) {

101:          for (uint256 i; i < bins.length; i++) {

```
https://github.com/code-423n4/2022-12-Stealth-Project/blob/fc8589d7d8c1d8488fd97ccc46e1ff11c8426ac2/maverick-v1/contracts/models/PoolInspector.sol#L30

```solidity
File: maverick-v1/contracts/models/Pool.sol

127:          for (uint256 i; i < params.length; i++) {

180:          for (uint256 i; i < binIds.length; i++) {

193:          for (uint256 i; i < params.length; i++) {

224:          for (uint256 i; i < params.length; i++) {

380:          for (uint256 j; j < 2; j++) {

389:              for (uint256 i; i <= moveData.binCounter; i++) {

414:              for (uint256 i; i < moveData.mergeBinCounter; i++) {

523:          for (uint256 i; i < NUMBER_OF_KINDS; i++) {

540:          for (uint256 i; i < NUMBER_OF_KINDS; i++) {

653:              for (uint256 i; i < swapData.counter; i++) {

```
https://github.com/code-423n4/2022-12-Stealth-Project/blob/fc8589d7d8c1d8488fd97ccc46e1ff11c8426ac2/maverick-v1/contracts/models/Pool.sol#L127

```solidity
File: router-v1/contracts/libraries/Multicall.sol

13:           for (uint256 i = 0; i < data.length; i++) {

```
https://github.com/code-423n4/2022-12-Stealth-Project/blob/fc8589d7d8c1d8488fd97ccc46e1ff11c8426ac2/router-v1/contracts/libraries/Multicall.sol#L13

### [G&#x2011;10]  `require()`/`revert()` strings longer than 32 bytes cost extra gas
Each extra memory word of bytes past the original 32 [incurs an MSTORE](https://gist.github.com/hrkrshnn/ee8fabd532058307229d65dcd5836ddc#consider-having-short-revert-strings) which costs **3 gas**

*There are 4 instances of this issue:*

```solidity
File: maverick-v1/contracts/models/Factory.sol

27:           require(_protocolFeeRatio <= ONE_3_DECIMAL_SCALE, "Factory:PROTOCOL_FEE_CANNOT_EXCEED_ONE");

35:           require(_protocolFeeRatio <= ONE_3_DECIMAL_SCALE, "Factory:PROTOCOL_FEE_CANNOT_EXCEED_ONE");

59:           require(_tokenA < _tokenB, "Factory:TOKENS_MUST_BE_SORTED_BY_ADDRESS");

61:           require(_tickSpacing > 0, "Factory:TICK_SPACING_OUT_OF_BOUNDS");

```
https://github.com/code-423n4/2022-12-Stealth-Project/blob/fc8589d7d8c1d8488fd97ccc46e1ff11c8426ac2/maverick-v1/contracts/models/Factory.sol#L27

### [G&#x2011;11]  Optimize names to save gas
`public`/`external` function names and `public` member variable names can be optimized to save gas. See [this](https://gist.github.com/IllIllI000/a5d8b486a8259f9f77891a919febd1a9) link for an example of how it works. Below are the interfaces/abstract contracts that can be optimized so that the most frequently-called functions use the least amount of gas possible during method lookup. Method IDs that have two leading zero bytes can save **128 gas** each during deployment, and renaming functions to have lower method IDs will save **22 gas** per call, [per sorted position shifted](https://medium.com/joyso/solidity-how-does-function-name-affect-gas-consumption-in-smart-contract-47d270d8ac92)

*There are 17 instances of this issue:*

```solidity
File: maverick-v1/contracts/interfaces/IAddLiquidityCallback.sol

/// @audit addLiquidityCallback()
4:    interface IAddLiquidityCallback {

```
https://github.com/code-423n4/2022-12-Stealth-Project/blob/fc8589d7d8c1d8488fd97ccc46e1ff11c8426ac2/maverick-v1/contracts/interfaces/IAddLiquidityCallback.sol#L4

```solidity
File: maverick-v1/contracts/interfaces/IFactory.sol

/// @audit create(), lookup(), position(), protocolFeeRatio(), isFactoryPool()
8:    interface IFactory {

```
https://github.com/code-423n4/2022-12-Stealth-Project/blob/fc8589d7d8c1d8488fd97ccc46e1ff11c8426ac2/maverick-v1/contracts/interfaces/IFactory.sol#L8

```solidity
File: maverick-v1/contracts/interfaces/IPoolAdmin.sol

/// @audit adminAction()
4:    interface IPoolAdmin {

```
https://github.com/code-423n4/2022-12-Stealth-Project/blob/fc8589d7d8c1d8488fd97ccc46e1ff11c8426ac2/maverick-v1/contracts/interfaces/IPoolAdmin.sol#L4

```solidity
File: maverick-v1/contracts/interfaces/IPool.sol

/// @audit fee(), tickSpacing(), tokenA(), tokenB(), factory(), binMap(), binPositions(), binBalanceA(), binBalanceB(), getTwa(), getState(), addLiquidity(), transferLiquidity(), removeLiquidity(), migrateBinsUpStack(), swap(), getBin(), balanceOf()
7:    interface IPool {

```
https://github.com/code-423n4/2022-12-Stealth-Project/blob/fc8589d7d8c1d8488fd97ccc46e1ff11c8426ac2/maverick-v1/contracts/interfaces/IPool.sol#L7

```solidity
File: maverick-v1/contracts/interfaces/IPosition.sol

/// @audit mint(), tokenOfOwnerByIndexExists()
7:    interface IPosition is IERC721Enumerable {

```
https://github.com/code-423n4/2022-12-Stealth-Project/blob/fc8589d7d8c1d8488fd97ccc46e1ff11c8426ac2/maverick-v1/contracts/interfaces/IPosition.sol#L7

```solidity
File: maverick-v1/contracts/interfaces/ISwapCallback.sol

/// @audit swapCallback()
4:    interface ISwapCallback {

```
https://github.com/code-423n4/2022-12-Stealth-Project/blob/fc8589d7d8c1d8488fd97ccc46e1ff11c8426ac2/maverick-v1/contracts/interfaces/ISwapCallback.sol#L4

```solidity
File: maverick-v1/contracts/libraries/Deployer.sol

/// @audit deploy()
11:   library Deployer {

```
https://github.com/code-423n4/2022-12-Stealth-Project/blob/fc8589d7d8c1d8488fd97ccc46e1ff11c8426ac2/maverick-v1/contracts/libraries/Deployer.sol#L11

```solidity
File: maverick-v1/contracts/models/Factory.sol

/// @audit setProtocolFeeRatio(), setOwner()
14:   contract Factory is IFactory {

```
https://github.com/code-423n4/2022-12-Stealth-Project/blob/fc8589d7d8c1d8488fd97ccc46e1ff11c8426ac2/maverick-v1/contracts/models/Factory.sol#L14

```solidity
File: maverick-v1/contracts/models/PoolInspector.sol

/// @audit getActiveBins(), getBinDepth(), calculateSwap(), getPrice()
11:   contract PoolInspector is ISwapCallback {

```
https://github.com/code-423n4/2022-12-Stealth-Project/blob/fc8589d7d8c1d8488fd97ccc46e1ff11c8426ac2/maverick-v1/contracts/models/PoolInspector.sol#L11

```solidity
File: maverick-v1/contracts/models/Pool.sol

/// @audit swap(), adminAction()
22:   contract Pool is IPool, IPoolAdmin {

```
https://github.com/code-423n4/2022-12-Stealth-Project/blob/fc8589d7d8c1d8488fd97ccc46e1ff11c8426ac2/maverick-v1/contracts/models/Pool.sol#L22

```solidity
File: maverick-v1/contracts/models/PositionMetadata.sol

/// @audit setBaseURI()
8:    contract PositionMetadata is IPositionMetadata, Ownable {

```
https://github.com/code-423n4/2022-12-Stealth-Project/blob/fc8589d7d8c1d8488fd97ccc46e1ff11c8426ac2/maverick-v1/contracts/models/PositionMetadata.sol#L8

```solidity
File: maverick-v1/contracts/models/Position.sol

/// @audit setMetadata(), mint(), tokenOfOwnerByIndexExists()
12:   contract Position is ERC721, ERC721Enumerable, Ownable, IPosition {

```
https://github.com/code-423n4/2022-12-Stealth-Project/blob/fc8589d7d8c1d8488fd97ccc46e1ff11c8426ac2/maverick-v1/contracts/models/Position.sol#L12

```solidity
File: router-v1/contracts/interfaces/external/IERC20PermitAllowed.sol

/// @audit permit()
6:    interface IERC20PermitAllowed {

```
https://github.com/code-423n4/2022-12-Stealth-Project/blob/fc8589d7d8c1d8488fd97ccc46e1ff11c8426ac2/router-v1/contracts/interfaces/external/IERC20PermitAllowed.sol#L6

```solidity
File: router-v1/contracts/interfaces/IMulticall.sol

/// @audit multicall()
7:    interface IMulticall {

```
https://github.com/code-423n4/2022-12-Stealth-Project/blob/fc8589d7d8c1d8488fd97ccc46e1ff11c8426ac2/router-v1/contracts/interfaces/IMulticall.sol#L7

```solidity
File: router-v1/contracts/interfaces/IRouter.sol

/// @audit factory(), position(), WETH9(), exactInputSingle(), exactInput(), exactOutputSingle(), exactOutput(), unwrapWETH9(), refundETH(), sweepToken(), getOrCreatePoolAndAddLiquidity(), addLiquidityToPool(), addLiquidityWTickLimits(), migrateBinsUpStack(), removeLiquidity()
11:   interface IRouter is ISwapCallback {

```
https://github.com/code-423n4/2022-12-Stealth-Project/blob/fc8589d7d8c1d8488fd97ccc46e1ff11c8426ac2/router-v1/contracts/interfaces/IRouter.sol#L11

```solidity
File: router-v1/contracts/interfaces/ISelfPermit.sol

/// @audit selfPermit(), selfPermitIfNecessary(), selfPermitAllowed(), selfPermitAllowedIfNecessary()
6:    interface ISelfPermit {

```
https://github.com/code-423n4/2022-12-Stealth-Project/blob/fc8589d7d8c1d8488fd97ccc46e1ff11c8426ac2/router-v1/contracts/interfaces/ISelfPermit.sol#L6

```solidity
File: router-v1/contracts/Router.sol

/// @audit sweepToken(), swapCallback(), addLiquidityCallback(), addLiquidityToPool(), addLiquidityWTickLimits(), getOrCreatePoolAndAddLiquidity(), migrateBinsUpStack(), removeLiquidity()
18:   contract Router is IRouter, Multicall, SelfPermit, Deadline {

```
https://github.com/code-423n4/2022-12-Stealth-Project/blob/fc8589d7d8c1d8488fd97ccc46e1ff11c8426ac2/router-v1/contracts/Router.sol#L18

### [G&#x2011;12]  Use a more recent version of solidity
Use a solidity version of at least 0.8.2 to get simple compiler automatic inlining
Use a solidity version of at least 0.8.3 to get better struct packing and cheaper multiple storage reads
Use a solidity version of at least 0.8.4 to get custom errors, which are cheaper at deployment than `revert()/require()` strings
Use a solidity version of at least 0.8.10 to have external calls skip contract existence checks if the external call has a return value

*There are 18 instances of this issue:*

```solidity
File: maverick-v1/contracts/libraries/BinMap.sol

2:    pragma solidity ^0.8.0;

```
https://github.com/code-423n4/2022-12-Stealth-Project/blob/fc8589d7d8c1d8488fd97ccc46e1ff11c8426ac2/maverick-v1/contracts/libraries/BinMap.sol#L2

```solidity
File: maverick-v1/contracts/libraries/BinMath.sol

2:    pragma solidity ^0.8.0;

```
https://github.com/code-423n4/2022-12-Stealth-Project/blob/fc8589d7d8c1d8488fd97ccc46e1ff11c8426ac2/maverick-v1/contracts/libraries/BinMath.sol#L2

```solidity
File: maverick-v1/contracts/libraries/Bin.sol

2:    pragma solidity ^0.8.0;

```
https://github.com/code-423n4/2022-12-Stealth-Project/blob/fc8589d7d8c1d8488fd97ccc46e1ff11c8426ac2/maverick-v1/contracts/libraries/Bin.sol#L2

```solidity
File: maverick-v1/contracts/libraries/Cast.sol

2:    pragma solidity ^0.8.0;

```
https://github.com/code-423n4/2022-12-Stealth-Project/blob/fc8589d7d8c1d8488fd97ccc46e1ff11c8426ac2/maverick-v1/contracts/libraries/Cast.sol#L2

```solidity
File: maverick-v1/contracts/libraries/Constants.sol

2:    pragma solidity ^0.8.0;

```
https://github.com/code-423n4/2022-12-Stealth-Project/blob/fc8589d7d8c1d8488fd97ccc46e1ff11c8426ac2/maverick-v1/contracts/libraries/Constants.sol#L2

```solidity
File: maverick-v1/contracts/libraries/Delta.sol

2:    pragma solidity ^0.8.0;

```
https://github.com/code-423n4/2022-12-Stealth-Project/blob/fc8589d7d8c1d8488fd97ccc46e1ff11c8426ac2/maverick-v1/contracts/libraries/Delta.sol#L2

```solidity
File: maverick-v1/contracts/libraries/Deployer.sol

2:    pragma solidity ^0.8.0;

```
https://github.com/code-423n4/2022-12-Stealth-Project/blob/fc8589d7d8c1d8488fd97ccc46e1ff11c8426ac2/maverick-v1/contracts/libraries/Deployer.sol#L2

```solidity
File: maverick-v1/contracts/libraries/Math.sol

2:    pragma solidity ^0.8.0;

```
https://github.com/code-423n4/2022-12-Stealth-Project/blob/fc8589d7d8c1d8488fd97ccc46e1ff11c8426ac2/maverick-v1/contracts/libraries/Math.sol#L2

```solidity
File: maverick-v1/contracts/libraries/SafeERC20Min.sol

3:    pragma solidity ^0.8.0;

```
https://github.com/code-423n4/2022-12-Stealth-Project/blob/fc8589d7d8c1d8488fd97ccc46e1ff11c8426ac2/maverick-v1/contracts/libraries/SafeERC20Min.sol#L3

```solidity
File: maverick-v1/contracts/libraries/Twa.sol

2:    pragma solidity ^0.8.0;

```
https://github.com/code-423n4/2022-12-Stealth-Project/blob/fc8589d7d8c1d8488fd97ccc46e1ff11c8426ac2/maverick-v1/contracts/libraries/Twa.sol#L2

```solidity
File: maverick-v1/contracts/models/Factory.sol

2:    pragma solidity ^0.8.0;

```
https://github.com/code-423n4/2022-12-Stealth-Project/blob/fc8589d7d8c1d8488fd97ccc46e1ff11c8426ac2/maverick-v1/contracts/models/Factory.sol#L2

```solidity
File: maverick-v1/contracts/models/PoolInspector.sol

2:    pragma solidity ^0.8.0;

```
https://github.com/code-423n4/2022-12-Stealth-Project/blob/fc8589d7d8c1d8488fd97ccc46e1ff11c8426ac2/maverick-v1/contracts/models/PoolInspector.sol#L2

```solidity
File: maverick-v1/contracts/models/Pool.sol

2:    pragma solidity ^0.8.0;

```
https://github.com/code-423n4/2022-12-Stealth-Project/blob/fc8589d7d8c1d8488fd97ccc46e1ff11c8426ac2/maverick-v1/contracts/models/Pool.sol#L2

```solidity
File: router-v1/contracts/interfaces/external/IWETH9.sol

2:    pragma solidity ^0.8.0;

```
https://github.com/code-423n4/2022-12-Stealth-Project/blob/fc8589d7d8c1d8488fd97ccc46e1ff11c8426ac2/router-v1/contracts/interfaces/external/IWETH9.sol#L2

```solidity
File: router-v1/contracts/libraries/Deadline.sol

2:    pragma solidity ^0.8.0;

```
https://github.com/code-423n4/2022-12-Stealth-Project/blob/fc8589d7d8c1d8488fd97ccc46e1ff11c8426ac2/router-v1/contracts/libraries/Deadline.sol#L2

```solidity
File: router-v1/contracts/libraries/Multicall.sol

2:    pragma solidity ^0.8.0;

```
https://github.com/code-423n4/2022-12-Stealth-Project/blob/fc8589d7d8c1d8488fd97ccc46e1ff11c8426ac2/router-v1/contracts/libraries/Multicall.sol#L2

```solidity
File: router-v1/contracts/libraries/Path.sol

2:    pragma solidity ^0.8.0;

```
https://github.com/code-423n4/2022-12-Stealth-Project/blob/fc8589d7d8c1d8488fd97ccc46e1ff11c8426ac2/router-v1/contracts/libraries/Path.sol#L2

```solidity
File: router-v1/contracts/Router.sol

2:    pragma solidity ^0.8.0;

```
https://github.com/code-423n4/2022-12-Stealth-Project/blob/fc8589d7d8c1d8488fd97ccc46e1ff11c8426ac2/router-v1/contracts/Router.sol#L2

### [G&#x2011;13]  `>=` costs less gas than `>`
The compiler uses opcodes `GT` and `ISZERO` for solidity code that uses `>`, but only requires `LT` for `>=`, [which saves **3 gas**](https://gist.github.com/IllIllI000/3dc79d25acccfa16dee4e83ffdc6ffde)

*There are 2 instances of this issue:*

```solidity
File: maverick-v1/contracts/libraries/Math.sol

9:            return x > y ? x : y;

17:           return x > y ? x : y;

```
https://github.com/code-423n4/2022-12-Stealth-Project/blob/fc8589d7d8c1d8488fd97ccc46e1ff11c8426ac2/maverick-v1/contracts/libraries/Math.sol#L9

### [G&#x2011;14]  `++i` costs less gas than `i++`, especially when it's used in `for`-loops (`--i`/`i--` too)
Saves **5 gas per loop**

*There is 1 instance of this issue:*

```solidity
File: maverick-v1/contracts/models/PoolInspector.sol

85:                   binCounter--;

```
https://github.com/code-423n4/2022-12-Stealth-Project/blob/fc8589d7d8c1d8488fd97ccc46e1ff11c8426ac2/maverick-v1/contracts/models/PoolInspector.sol#L85

### [G&#x2011;15]  Splitting `require()` statements that use `&&` saves gas
See [this issue](https://github.com/code-423n4/2022-01-xdefi-findings/issues/128) which describes the fact that there is a larger deployment gas cost, but with enough runtime calls, the change ends up being cheaper by **3 gas**

*There are 12 instances of this issue:*

```solidity
File: maverick-v1/contracts/models/Factory.sol

41:           require(msg.sender == owner && _owner != address(0), "Factory:NOT_ALLOWED");

60:           require(_fee > 0 && _fee < 1e18, "Factory:FEE_OUT_OF_BOUNDS");

62:           require(_lookback >= 3600 && _lookback <= uint16(type(int16).max), "Factory:LOOKBACK_OUT_OF_BOUNDS");

```
https://github.com/code-423n4/2022-12-Stealth-Project/blob/fc8589d7d8c1d8488fd97ccc46e1ff11c8426ac2/maverick-v1/contracts/models/Factory.sol#L41

```solidity
File: maverick-v1/contracts/models/Pool.sol

93:           require((currentState.status & LOCKED == 0) && (allowInEmergency || (currentState.status & EMERGENCY == 0)), "E");

168:          require(previousABalance + tokenAAmount <= _tokenABalance() && previousBBalance + tokenBAmount <= _tokenBBalance(), "A");

```
https://github.com/code-423n4/2022-12-Stealth-Project/blob/fc8589d7d8c1d8488fd97ccc46e1ff11c8426ac2/maverick-v1/contracts/models/Pool.sol#L93

```solidity
File: router-v1/contracts/libraries/TransferHelper.sol

15:           require(success && (data.length == 0 || abi.decode(data, (bool))), "STF");

25:           require(success && (data.length == 0 || abi.decode(data, (bool))), "ST");

35:           require(success && (data.length == 0 || abi.decode(data, (bool))), "SA");

```
https://github.com/code-423n4/2022-12-Stealth-Project/blob/fc8589d7d8c1d8488fd97ccc46e1ff11c8426ac2/router-v1/contracts/libraries/TransferHelper.sol#L15

```solidity
File: router-v1/contracts/Router.sol

100:          require(amountToPay > 0 && amountOut > 0, "In or Out Amount is Zero");

234:          require(tokenAAmount >= minTokenAAmount && tokenBAmount >= minTokenBAmount, "Too little added");

262:          require(activeTick >= minActiveTick && activeTick <= maxActiveTick, "activeTick not in range");

309:          require(tokenAAmount >= minTokenAAmount && tokenBAmount >= minTokenBAmount, "Too little removed");

```
https://github.com/code-423n4/2022-12-Stealth-Project/blob/fc8589d7d8c1d8488fd97ccc46e1ff11c8426ac2/router-v1/contracts/Router.sol#L100

### [G&#x2011;16]  Usage of `uints`/`ints` smaller than 32 bytes (256 bits) incurs overhead
> When using elements that are smaller than 32 bytes, your contractâ€™s gas usage may be higher. This is because the EVM operates on 32 bytes at a time. Therefore, if the element is smaller than that, the EVM must use more operations in order to reduce the size of the element from 32 bytes to the desired size.

https://docs.soliditylang.org/en/v0.8.11/internals/layout_in_storage.html
Each operation involving a `uint8` costs an extra [**22-28 gas**](https://gist.github.com/IllIllI000/9388d20c70f9a4632eb3ca7836f54977) (depending on whether the other operand is also a variable of type `uint8`) as compared to ones involving `uint256`, due to the compiler having to clear the higher bits of the memory word before operating on the `uint8`, as well as the associated stack operations of doing so. Use a larger size then downcast where needed

*There are 50 instances of this issue:*

```solidity
File: maverick-v1/contracts/libraries/BinMap.sol

/// @audit uint8 offset
21:           offset = uint8(uint32(tick_kinds));

/// @audit int32 mapIndex
22:           mapIndex = tick_kinds >> 8;

/// @audit uint16 offset
54:                   offset += bitIndex;

/// @audit uint16 offset
68:                   offset += uint16(uint32(NUMBER_OF_KINDS_32));

/// @audit int32 wordIndex
70:               wordIndex += 1;

/// @audit uint16 offset
71:               offset = 0;

/// @audit int32 tack
86:               tack = 1;

/// @audit uint16 shift
88:               shift = uint16(WORD_SIZE - offset);

/// @audit int32 tack
89:               tack = -1;

/// @audit uint16 shift
96:               shift = 0;

/// @audit uint16 subIndex
100:              subIndex = isRight ? BinMath.lsb(nextWord) + shift : BinMath.msb(nextWord) - shift;

/// @audit int32 nextTick
103:              nextTick = posFirst >> 2;

```
https://github.com/code-423n4/2022-12-Stealth-Project/blob/fc8589d7d8c1d8488fd97ccc46e1ff11c8426ac2/maverick-v1/contracts/libraries/BinMap.sol#L21

```solidity
File: maverick-v1/contracts/libraries/BinMath.sol

/// @audit uint8 result
14:               result = 0;

/// @audit uint8 result
18:                   result += 128;

/// @audit uint8 result
22:                   result += 64;

/// @audit uint8 result
26:                   result += 32;

/// @audit uint8 result
30:                   result += 16;

/// @audit uint8 result
34:                   result += 8;

/// @audit uint8 result
38:                   result += 4;

/// @audit uint8 result
42:                   result += 2;

/// @audit uint8 result
44:               if (x >= 0x2) result += 1;

/// @audit uint8 result
50:               result = 255;

/// @audit uint8 result
53:                   result -= 128;

/// @audit uint8 result
58:                   result -= 64;

/// @audit uint8 result
63:                   result -= 32;

/// @audit uint8 result
68:                   result -= 16;

/// @audit uint8 result
73:                   result -= 8;

/// @audit uint8 result
78:                   result -= 4;

/// @audit uint8 result
83:                   result -= 2;

/// @audit uint8 result
87:               if (x & 0x1 > 0) result -= 1;

```
https://github.com/code-423n4/2022-12-Stealth-Project/blob/fc8589d7d8c1d8488fd97ccc46e1ff11c8426ac2/maverick-v1/contracts/libraries/BinMath.sol#L14

```solidity
File: maverick-v1/contracts/libraries/Bin.sol

/// @audit uint128 deltaIn
61:           deltaIn = Math.mulDiv(delta.deltaInBinInternal, thisBinAmount, totalAmount, false).toUint128();

/// @audit uint128 deltaOut
63:               deltaOut = Math.mulDiv(delta.deltaOutErc, thisBinAmount, totalAmount, true).toUint128();

/// @audit uint32 maxRecursion
97:           maxRecursion = maxRecursion == 0 ? type(uint32).max : maxRecursion;

/// @audit uint32 maxRecursion
114:              maxRecursion -= 1;

/// @audit uint128 delta
122:          delta = Math.min(Math.mulDiv(tokenAmount, reserve, totalSupply, false), reserve).toUint128();

/// @audit uint128 reserveOut
123:          reserveOut = delta == 0 ? reserve : Math.clip128(reserve, delta + 1);

/// @audit uint128 deltaLpBalance128
146:              deltaLpBalance128 = Math.min(self.state.mergeBinBalance, Math.mulDiv(deltaLpBalance, self.state.mergeBinBalance, tempTotalSupply, false)).toUint128();

```
https://github.com/code-423n4/2022-12-Stealth-Project/blob/fc8589d7d8c1d8488fd97ccc46e1ff11c8426ac2/maverick-v1/contracts/libraries/Bin.sol#L61

```solidity
File: maverick-v1/contracts/libraries/Cast.sol

/// @audit uint128 y
6:            require((y = uint128(x)) == x, "C");

```
https://github.com/code-423n4/2022-12-Stealth-Project/blob/fc8589d7d8c1d8488fd97ccc46e1ff11c8426ac2/maverick-v1/contracts/libraries/Cast.sol#L6

```solidity
File: maverick-v1/contracts/models/PoolInspector.sol

/// @audit uint128 binCounter
26:               binCounter = binCounter < endBinIndex ? binCounter : endBinIndex;

/// @audit uint128 binId
50:               binId = bin.mergeId;

```
https://github.com/code-423n4/2022-12-Stealth-Project/blob/fc8589d7d8c1d8488fd97ccc46e1ff11c8426ac2/maverick-v1/contracts/models/PoolInspector.sol#L26

```solidity
File: maverick-v1/contracts/models/Pool.sol

/// @audit uint128 binBalanceA
161:          binBalanceA += tokenAAmount.toUint128();

/// @audit uint128 binBalanceB
162:          binBalanceB += tokenBAmount.toUint128();

/// @audit uint128 binBalanceA
242:          binBalanceA = Math.clip128(binBalanceA, tokenAOut.toUint128());

/// @audit uint128 binBalanceB
243:          binBalanceB = Math.clip128(binBalanceB, tokenBOut.toUint128());

/// @audit uint128 binBalanceA
288:                  binBalanceA += delta.deltaInBinInternal.toUint128();

/// @audit uint128 binBalanceB
289:                  binBalanceB = Math.clip128(binBalanceB, delta.deltaOutErc.toUint128());

/// @audit uint128 binBalanceB
291:                  binBalanceB += delta.deltaInBinInternal.toUint128();

/// @audit uint128 binBalanceA
292:                  binBalanceA = Math.clip128(binBalanceA, delta.deltaOutErc.toUint128());

/// @audit uint128 binId
492:              binId = currentState.binCounter;

/// @audit int32 activeTick
622:                  activeTick = binMap.nextActive(activeTick, delta.tokenAIn);

```
https://github.com/code-423n4/2022-12-Stealth-Project/blob/fc8589d7d8c1d8488fd97ccc46e1ff11c8426ac2/maverick-v1/contracts/models/Pool.sol#L161

### [G&#x2011;17]  Inverting the condition of an `if`-`else`-statement wastes gas
Flipping the `true` and `false` blocks instead saves ***[3 gas](https://gist.github.com/IllIllI000/44da6fbe9d12b9ab21af82f14add56b9)***

*There is 1 instance of this issue:*

```solidity
File: maverick-v1/contracts/models/Pool.sol

270:              delta.excess = (!exactOutput) ? Math.fromScale(amount, tokenAIn ? tokenAScale : tokenBScale) : Math.fromScale(amount, !tokenAIn ? tokenAScale : tokenBScale);

```
https://github.com/code-423n4/2022-12-Stealth-Project/blob/fc8589d7d8c1d8488fd97ccc46e1ff11c8426ac2/maverick-v1/contracts/models/Pool.sol#L270

### [G&#x2011;18]  `require()` or `revert()` statements that check input arguments should be at the top of the function
Checks that involve constants should come before checks that involve state variables, function calls, and calculations. By doing these checks first, the function is able to revert before wasting a Gcoldsload (**2100 gas***) in a function that may ultimately revert in the unhappy case.

*There are 2 instances of this issue:*

```solidity
File: maverick-v1/contracts/models/Factory.sol

/// @audit expensive op on line 34
35:           require(_protocolFeeRatio <= ONE_3_DECIMAL_SCALE, "Factory:PROTOCOL_FEE_CANNOT_EXCEED_ONE");

/// @audit expensive op on line 60
61:           require(_tickSpacing > 0, "Factory:TICK_SPACING_OUT_OF_BOUNDS");

```
https://github.com/code-423n4/2022-12-Stealth-Project/blob/fc8589d7d8c1d8488fd97ccc46e1ff11c8426ac2/maverick-v1/contracts/models/Factory.sol#L35

### [G&#x2011;19]  Functions guaranteed to revert when called by normal users can be marked `payable`
If a function modifier such as `onlyOwner` is used, the function will revert if a normal user tries to pay the function. Marking the function as `payable` will lower the gas cost for legitimate callers because the compiler will not include checks for whether a payment was provided. The extra opcodes avoided are 
`CALLVALUE`(2),`DUP1`(3),`ISZERO`(3),`PUSH2`(3),`JUMPI`(10),`PUSH1`(3),`DUP1`(3),`REVERT`(0),`JUMPDEST`(1),`POP`(2), which costs an average of about **21 gas per call** to the function, in addition to the extra deployment cost

*There are 2 instances of this issue:*

```solidity
File: maverick-v1/contracts/models/PositionMetadata.sol

17:       function setBaseURI(string memory _baseURI) external onlyOwner {

```
https://github.com/code-423n4/2022-12-Stealth-Project/blob/fc8589d7d8c1d8488fd97ccc46e1ff11c8426ac2/maverick-v1/contracts/models/PositionMetadata.sol#L17

```solidity
File: maverick-v1/contracts/models/Position.sol

23:       function setMetadata(IPositionMetadata _metadata) external onlyOwner {

```
https://github.com/code-423n4/2022-12-Stealth-Project/blob/fc8589d7d8c1d8488fd97ccc46e1ff11c8426ac2/maverick-v1/contracts/models/Position.sol#L23


___

## Excluded findings
These findings are excluded from awards calculations because there are publicly-available automated tools that find them. The valid ones appear here for completeness

## Summary

### Gas Optimizations
| |Issue|Instances|Total Gas Saved|
|-|:-|:-:|:-:|
| [G&#x2011;01] | Using `calldata` instead of `memory` for read-only arguments in `external` functions saves gas | 2 |  240 |
| [G&#x2011;02] | State variables should be cached in stack variables rather than re-reading them from storage | 2 |  194 |
| [G&#x2011;03] | `<array>.length` should not be looked up in every loop of a `for`-loop | 6 |  18 |
| [G&#x2011;04] | Using `bool`s for storage incurs overhead | 1 |  17100 |
| [G&#x2011;05] | Using `> 0` costs more gas than `!= 0` when used on a `uint` in a `require()` statement | 1 |  6 |
| [G&#x2011;06] | `++i` costs less gas than `i++`, especially when it's used in `for`-loops (`--i`/`i--` too) | 19 |  95 |
| [G&#x2011;07] | Use custom errors rather than `revert()`/`require()` strings to save gas | 38 |  - |

Total: 69 instances over 7 issues with **17653 gas** saved

Gas totals use lower bounds of ranges and count two iterations of each `for`-loop. All values above are runtime, not deployment, values; deployment values are listed in the individual issue descriptions. The table above as well as its gas numbers do not include any of the excluded findings.





## Gas Optimizations

### [G&#x2011;01]  Using `calldata` instead of `memory` for read-only arguments in `external` functions saves gas
When a function with a `memory` array is called externally, the `abi.decode()` step has to use a for-loop to copy each index of the `calldata` to the `memory` index. **Each iteration of this for-loop costs at least 60 gas** (i.e. `60 * <mem_array>.length`). Using `calldata` directly, obliviates the need for such a loop in the contract code and runtime execution. Note that even if an interface defines a function as having `memory` arguments, it's still valid for implementation contracs to use `calldata` arguments instead. 

If the array is passed to an `internal` function which passes the array to another internal function where the array is modified and therefore `memory` is used in the `external` call, it's still more gass-efficient to use `calldata` when the `external` function uses modifiers, since the modifiers may prevent the internal functions from being called. Structs have the same overhead as an array of length one

Note that I've also flagged instances where the function is `public` but can be marked as `external` since it's not called by the contract, and cases where a constructor is involved

*There are 2 instances of this issue:*

```solidity
File: maverick-v1/contracts/models/PositionMetadata.sol

/// @audit _baseURI - (valid but excluded finding)
17:       function setBaseURI(string memory _baseURI) external onlyOwner {

```
https://github.com/code-423n4/2022-12-Stealth-Project/blob/fc8589d7d8c1d8488fd97ccc46e1ff11c8426ac2/maverick-v1/contracts/models/PositionMetadata.sol#L17

```solidity
File: router-v1/contracts/Router.sol

/// @audit params - (valid but excluded finding)
143:      function exactInput(ExactInputParams memory params) external payable override checkDeadline(params.deadline) returns (uint256 amountOut) {

```
https://github.com/code-423n4/2022-12-Stealth-Project/blob/fc8589d7d8c1d8488fd97ccc46e1ff11c8426ac2/router-v1/contracts/Router.sol#L143

### [G&#x2011;02]  State variables should be cached in stack variables rather than re-reading them from storage
The instances below point to the second+ access of a state variable within a function. Caching of a state variable replaces each Gwarmaccess (**100 gas**) with a much cheaper stack read. Other less obvious fixes/optimizations include having local memory caches of state variable structs, or having local caches of state variable contracts/addresses.

*There are 2 instances of this issue:*

```solidity
File: maverick-v1/contracts/models/Factory.sol

/// @audit protocolFeeRatio on line 71 - (valid but excluded finding)
80:           emit PoolCreated(address(pool), _fee, _tickSpacing, _activeTick, _lookback, protocolFeeRatio, _tokenA, _tokenB);

```
https://github.com/code-423n4/2022-12-Stealth-Project/blob/fc8589d7d8c1d8488fd97ccc46e1ff11c8426ac2/maverick-v1/contracts/models/Factory.sol#L80

```solidity
File: maverick-v1/contracts/models/PositionMetadata.sol

/// @audit baseURI on line 22 - (valid but excluded finding)
22:           return bytes(baseURI).length > 0 ? string(abi.encodePacked(baseURI, tokenId.toString())) : "";

```
https://github.com/code-423n4/2022-12-Stealth-Project/blob/fc8589d7d8c1d8488fd97ccc46e1ff11c8426ac2/maverick-v1/contracts/models/PositionMetadata.sol#L22

### [G&#x2011;03]  `<array>.length` should not be looked up in every loop of a `for`-loop
The overheads outlined below are _PER LOOP_, excluding the first loop
* storage arrays incur a Gwarmaccess (**100 gas**)
* memory arrays use `MLOAD` (**3 gas**)
* calldata arrays use `CALLDATALOAD` (**3 gas**)

Caching the length changes each of these to a `DUP<N>` (**3 gas**), and gets rid of the extra `DUP<N>` needed to store the stack offset

*There are 6 instances of this issue:*

```solidity
File: maverick-v1/contracts/models/PoolInspector.sol

/// @audit (valid but excluded finding)
101:          for (uint256 i; i < bins.length; i++) {

```
https://github.com/code-423n4/2022-12-Stealth-Project/blob/fc8589d7d8c1d8488fd97ccc46e1ff11c8426ac2/maverick-v1/contracts/models/PoolInspector.sol#L101

```solidity
File: maverick-v1/contracts/models/Pool.sol

/// @audit (valid but excluded finding)
127:          for (uint256 i; i < params.length; i++) {

/// @audit (valid but excluded finding)
180:          for (uint256 i; i < binIds.length; i++) {

/// @audit (valid but excluded finding)
193:          for (uint256 i; i < params.length; i++) {

/// @audit (valid but excluded finding)
224:          for (uint256 i; i < params.length; i++) {

```
https://github.com/code-423n4/2022-12-Stealth-Project/blob/fc8589d7d8c1d8488fd97ccc46e1ff11c8426ac2/maverick-v1/contracts/models/Pool.sol#L127

```solidity
File: router-v1/contracts/libraries/Multicall.sol

/// @audit (valid but excluded finding)
13:           for (uint256 i = 0; i < data.length; i++) {

```
https://github.com/code-423n4/2022-12-Stealth-Project/blob/fc8589d7d8c1d8488fd97ccc46e1ff11c8426ac2/router-v1/contracts/libraries/Multicall.sol#L13

### [G&#x2011;04]  Using `bool`s for storage incurs overhead
```solidity
    // Booleans are more expensive than uint256 or any type that takes up a full
    // word because each write operation emits an extra SLOAD to first read the
    // slot's contents, replace the bits taken up by the boolean, and then write
    // back. This is the compiler's defense against contract upgrades and
    // pointer aliasing, and it cannot be disabled.
```
https://github.com/OpenZeppelin/openzeppelin-contracts/blob/58f635312aa21f947cae5f8578638a85aa2519f5/contracts/security/ReentrancyGuard.sol#L23-L27
Use `uint256(1)` and `uint256(2)` for true/false to avoid a Gwarmaccess (**[100 gas](https://gist.github.com/IllIllI000/1b70014db712f8572a72378321250058)**) for the extra SLOAD, and to avoid Gsset (**20000 gas**) when changing from `false` to `true`, after having been `true` in the past

*There is 1 instance of this issue:*

```solidity
File: maverick-v1/contracts/models/Factory.sol

/// @audit (valid but excluded finding)
18:       mapping(IPool => bool) public override isFactoryPool;

```
https://github.com/code-423n4/2022-12-Stealth-Project/blob/fc8589d7d8c1d8488fd97ccc46e1ff11c8426ac2/maverick-v1/contracts/models/Factory.sol#L18

### [G&#x2011;05]  Using `> 0` costs more gas than `!= 0` when used on a `uint` in a `require()` statement
This change saves **[6 gas](https://aws1.discourse-cdn.com/business6/uploads/zeppelin/original/2X/3/363a367d6d68851f27d2679d10706cd16d788b96.png)** per instance. The optimization works until solidity version [0.8.13](https://gist.github.com/IllIllI000/bf2c3120f24a69e489f12b3213c06c94) where there is a regression in gas costs.

*There is 1 instance of this issue:*

```solidity
File: maverick-v1/contracts/models/Factory.sol

/// @audit (valid but excluded finding)
61:           require(_tickSpacing > 0, "Factory:TICK_SPACING_OUT_OF_BOUNDS");

```
https://github.com/code-423n4/2022-12-Stealth-Project/blob/fc8589d7d8c1d8488fd97ccc46e1ff11c8426ac2/maverick-v1/contracts/models/Factory.sol#L61

### [G&#x2011;06]  `++i` costs less gas than `i++`, especially when it's used in `for`-loops (`--i`/`i--` too)
Saves **5 gas per loop**

*There are 19 instances of this issue:*

```solidity
File: maverick-v1/contracts/models/PoolInspector.sol

/// @audit (valid but excluded finding)
30:           for (uint128 i = startBinIndex; i < binCounter; i++) {

/// @audit (valid but excluded finding)
34:                   activeCounter++;

/// @audit (valid but excluded finding)
49:               depth++;

/// @audit (valid but excluded finding)
80:           for (uint8 i = 0; i < NUMBER_OF_KINDS; i++) {

/// @audit (valid but excluded finding)
101:          for (uint256 i; i < bins.length; i++) {

```
https://github.com/code-423n4/2022-12-Stealth-Project/blob/fc8589d7d8c1d8488fd97ccc46e1ff11c8426ac2/maverick-v1/contracts/models/PoolInspector.sol#L30

```solidity
File: maverick-v1/contracts/models/Pool.sol

/// @audit (valid but excluded finding)
127:          for (uint256 i; i < params.length; i++) {

/// @audit (valid but excluded finding)
180:          for (uint256 i; i < binIds.length; i++) {

/// @audit (valid but excluded finding)
193:          for (uint256 i; i < params.length; i++) {

/// @audit (valid but excluded finding)
224:          for (uint256 i; i < params.length; i++) {

/// @audit (valid but excluded finding)
380:          for (uint256 j; j < 2; j++) {

/// @audit (valid but excluded finding)
389:              for (uint256 i; i <= moveData.binCounter; i++) {

/// @audit (valid but excluded finding)
396:                      moveData.mergeBinCounter++;

/// @audit (valid but excluded finding)
409:                      moveData.mergeBinCounter++;

/// @audit (valid but excluded finding)
414:              for (uint256 i; i < moveData.mergeBinCounter; i++) {

/// @audit (valid but excluded finding)
523:          for (uint256 i; i < NUMBER_OF_KINDS; i++) {

/// @audit (valid but excluded finding)
530:                  output.counter++;

/// @audit (valid but excluded finding)
540:          for (uint256 i; i < NUMBER_OF_KINDS; i++) {

/// @audit (valid but excluded finding)
653:              for (uint256 i; i < swapData.counter; i++) {

```
https://github.com/code-423n4/2022-12-Stealth-Project/blob/fc8589d7d8c1d8488fd97ccc46e1ff11c8426ac2/maverick-v1/contracts/models/Pool.sol#L127

```solidity
File: router-v1/contracts/libraries/Multicall.sol

/// @audit (valid but excluded finding)
13:           for (uint256 i = 0; i < data.length; i++) {

```
https://github.com/code-423n4/2022-12-Stealth-Project/blob/fc8589d7d8c1d8488fd97ccc46e1ff11c8426ac2/router-v1/contracts/libraries/Multicall.sol#L13

### [G&#x2011;07]  Use custom errors rather than `revert()`/`require()` strings to save gas
Custom errors are available from solidity version 0.8.4. Custom errors save [**~50 gas**](https://gist.github.com/IllIllI000/ad1bd0d29a0101b25e57c293b4b0c746) each time they're hit by [avoiding having to allocate and store the revert string](https://blog.soliditylang.org/2021/04/21/custom-errors/#errors-in-depth). Not defining the strings also save deployment gas

*There are 38 instances of this issue:*

```solidity
File: maverick-v1/contracts/libraries/BinMath.sol

/// @audit (valid but excluded finding)
94:           require(tick <= MAX_TICK, "X");

```
https://github.com/code-423n4/2022-12-Stealth-Project/blob/fc8589d7d8c1d8488fd97ccc46e1ff11c8426ac2/maverick-v1/contracts/libraries/BinMath.sol#L94

```solidity
File: maverick-v1/contracts/libraries/Bin.sol

/// @audit (valid but excluded finding)
85:           require(deltaLpToken != 0, "L");

/// @audit (valid but excluded finding)
140:              require(activeBin.state.mergeId == 0, "N");

```
https://github.com/code-423n4/2022-12-Stealth-Project/blob/fc8589d7d8c1d8488fd97ccc46e1ff11c8426ac2/maverick-v1/contracts/libraries/Bin.sol#L85

```solidity
File: maverick-v1/contracts/libraries/Cast.sol

/// @audit (valid but excluded finding)
6:            require((y = uint128(x)) == x, "C");

```
https://github.com/code-423n4/2022-12-Stealth-Project/blob/fc8589d7d8c1d8488fd97ccc46e1ff11c8426ac2/maverick-v1/contracts/libraries/Cast.sol#L6

```solidity
File: maverick-v1/contracts/libraries/SafeERC20Min.sol

/// @audit (valid but excluded finding)
18:               require(abi.decode(returndata, (bool)), "T");

```
https://github.com/code-423n4/2022-12-Stealth-Project/blob/fc8589d7d8c1d8488fd97ccc46e1ff11c8426ac2/maverick-v1/contracts/libraries/SafeERC20Min.sol#L18

```solidity
File: maverick-v1/contracts/models/Factory.sol

/// @audit (valid but excluded finding)
27:           require(_protocolFeeRatio <= ONE_3_DECIMAL_SCALE, "Factory:PROTOCOL_FEE_CANNOT_EXCEED_ONE");

/// @audit (valid but excluded finding)
34:           require(msg.sender == owner, "Factory:NOT_ALLOWED");

/// @audit (valid but excluded finding)
35:           require(_protocolFeeRatio <= ONE_3_DECIMAL_SCALE, "Factory:PROTOCOL_FEE_CANNOT_EXCEED_ONE");

/// @audit (valid but excluded finding)
41:           require(msg.sender == owner && _owner != address(0), "Factory:NOT_ALLOWED");

/// @audit (valid but excluded finding)
59:           require(_tokenA < _tokenB, "Factory:TOKENS_MUST_BE_SORTED_BY_ADDRESS");

/// @audit (valid but excluded finding)
60:           require(_fee > 0 && _fee < 1e18, "Factory:FEE_OUT_OF_BOUNDS");

/// @audit (valid but excluded finding)
61:           require(_tickSpacing > 0, "Factory:TICK_SPACING_OUT_OF_BOUNDS");

/// @audit (valid but excluded finding)
62:           require(_lookback >= 3600 && _lookback <= uint16(type(int16).max), "Factory:LOOKBACK_OUT_OF_BOUNDS");

/// @audit (valid but excluded finding)
64:           require(pools[_fee][_tickSpacing][_lookback][_tokenA][_tokenB] == IPool(address(0)), "Factory:POOL_ALREADY_EXISTS");

```
https://github.com/code-423n4/2022-12-Stealth-Project/blob/fc8589d7d8c1d8488fd97ccc46e1ff11c8426ac2/maverick-v1/contracts/models/Factory.sol#L27

```solidity
File: maverick-v1/contracts/models/Pool.sol

/// @audit (valid but excluded finding)
87:           require(msg.sender == position.ownerOf(tokenId) || msg.sender == position.getApproved(tokenId), "P");

/// @audit (valid but excluded finding)
93:           require((currentState.status & LOCKED == 0) && (allowInEmergency || (currentState.status & EMERGENCY == 0)), "E");

/// @audit (valid but excluded finding)
168:          require(previousABalance + tokenAAmount <= _tokenABalance() && previousBBalance + tokenBAmount <= _tokenBBalance(), "A");

/// @audit (valid but excluded finding)
303:          require(previousBalance + amountIn <= (tokenAIn ? _tokenABalance() : _tokenBBalance()), "S");

```
https://github.com/code-423n4/2022-12-Stealth-Project/blob/fc8589d7d8c1d8488fd97ccc46e1ff11c8426ac2/maverick-v1/contracts/models/Pool.sol#L87

```solidity
File: maverick-v1/contracts/models/Position.sol

/// @audit (valid but excluded finding)
43:           require(_exists(tokenId), "Invalid Token ID");

```
https://github.com/code-423n4/2022-12-Stealth-Project/blob/fc8589d7d8c1d8488fd97ccc46e1ff11c8426ac2/maverick-v1/contracts/models/Position.sol#L43

```solidity
File: router-v1/contracts/libraries/Deadline.sol

/// @audit (valid but excluded finding)
6:            require(block.timestamp <= deadline, "Transaction too old");

```
https://github.com/code-423n4/2022-12-Stealth-Project/blob/fc8589d7d8c1d8488fd97ccc46e1ff11c8426ac2/router-v1/contracts/libraries/Deadline.sol#L6

```solidity
File: router-v1/contracts/libraries/TransferHelper.sol

/// @audit (valid but excluded finding)
15:           require(success && (data.length == 0 || abi.decode(data, (bool))), "STF");

/// @audit (valid but excluded finding)
25:           require(success && (data.length == 0 || abi.decode(data, (bool))), "ST");

/// @audit (valid but excluded finding)
35:           require(success && (data.length == 0 || abi.decode(data, (bool))), "SA");

/// @audit (valid but excluded finding)
44:           require(success, "STE");

```
https://github.com/code-423n4/2022-12-Stealth-Project/blob/fc8589d7d8c1d8488fd97ccc46e1ff11c8426ac2/router-v1/contracts/libraries/TransferHelper.sol#L15

```solidity
File: router-v1/contracts/Router.sol

/// @audit (valid but excluded finding)
55:           require(IWETH9(msg.sender) == WETH9, "Not WETH9");

/// @audit (valid but excluded finding)
61:           require(balanceWETH9 >= amountMinimum, "Insufficient WETH9");

/// @audit (valid but excluded finding)
72:           require(balanceToken >= amountMinimum, "Insufficient token");

/// @audit (valid but excluded finding)
100:          require(amountToPay > 0 && amountOut > 0, "In or Out Amount is Zero");

/// @audit (valid but excluded finding)
101:          require(factory.isFactoryPool(IPool(msg.sender)), "Must call from a Factory Pool");

/// @audit (valid but excluded finding)
139:          require(amountOut >= params.amountOutMinimum, "Too little received");

/// @audit (valid but excluded finding)
165:          require(amountOut >= params.amountOutMinimum, "Too little received");

/// @audit (valid but excluded finding)
177:          require(amountOutReceived == amountOut, "Requested amount not available");

/// @audit (valid but excluded finding)
188:          require(amountIn <= params.amountInMaximum, "Too much requested");

/// @audit (valid but excluded finding)
197:          require(amountIn <= params.amountInMaximum, "Too much requested");

/// @audit (valid but excluded finding)
234:          require(tokenAAmount >= minTokenAAmount && tokenBAmount >= minTokenBAmount, "Too little added");

/// @audit (valid but excluded finding)
262:          require(activeTick >= minActiveTick && activeTick <= maxActiveTick, "activeTick not in range");

/// @audit (valid but excluded finding)
304:          require(msg.sender == position.ownerOf(tokenId), "P");

/// @audit (valid but excluded finding)
309:          require(tokenAAmount >= minTokenAAmount && tokenBAmount >= minTokenBAmount, "Too little removed");

```
https://github.com/code-423n4/2022-12-Stealth-Project/blob/fc8589d7d8c1d8488fd97ccc46e1ff11c8426ac2/router-v1/contracts/Router.sol#L55
