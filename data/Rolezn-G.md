## Summary<a name="Summary">

### Gas Optimizations
| |Issue|Contexts|Estimated Gas Saved|
|-|:-|:-|:-:|
| [GAS&#x2011;1](#GAS&#x2011;1) | Duplicated `require()`/`revert()` Checks Should Be Refactored To A Modifier Or Function | 6 | 168 |
| [GAS&#x2011;2](#GAS&#x2011;2) | Empty Blocks Should Be Removed Or Emit Something | 1 | - |
| [GAS&#x2011;3](#GAS&#x2011;3) | `<x> += <y>` Costs More Gas Than `<x> = <x> + <y>` For State Variables | 53 | - |
| [GAS&#x2011;4](#GAS&#x2011;4) | `++i`/`i++` Should Be `unchecked{++i}`/`unchecked{i++}` When It Is Not Possible For Them To Overflow, As Is The Case When Used In For- And While-loops | 14 | 490 |
| [GAS&#x2011;5](#GAS&#x2011;5) | `require()`/`revert()` Strings Longer Than 32 Bytes Cost Extra Gas | 6 | - |
| [GAS&#x2011;6](#GAS&#x2011;6) | Splitting `require()` Statements That Use `&&` Saves Gas | 12 | 108 |
| [GAS&#x2011;7](#GAS&#x2011;7) | `abi.encode()` is less efficient than `abi.encodepacked()` | 7 | 700 |
| [GAS&#x2011;8](#GAS&#x2011;8) | Public Functions To External | 8 | - |
| [GAS&#x2011;9](#GAS&#x2011;9) | Optimize names to save gas | 9 | 198 |
| [GAS&#x2011;10](#GAS&#x2011;10) | Do not calculate constants | 3 | - |
| [GAS&#x2011;11](#GAS&#x2011;11) | `internal` functions only called once can be inlined to save gas | 10 | - |
| [GAS&#x2011;12](#GAS&#x2011;12) | Setting the `constructor` to `payable` | 5 | 65 |
| [GAS&#x2011;13](#GAS&#x2011;13) | Functions guaranteed to revert when called by normal users can be marked `payable` | 2 | 42 |

Total: 136 contexts over 13 issues

## Gas Optimizations


#### <a href="#Summary">[GAS&#x2011;1]</a><a name="GAS&#x2011;1"> Duplicated `require()`/`revert()` Checks Should Be Refactored To A Modifier Or Function

Saves deployment costs

#### <ins>Proof Of Concept</ins>

```solidity
27: (_protocolFeeRatio <= ONE_3_DECIMAL_SCALE, "Factory:PROTOCOL_FEE_CANNOT_EXCEED_ONE");
35: (_protocolFeeRatio <= ONE_3_DECIMAL_SCALE, "Factory:PROTOCOL_FEE_CANNOT_EXCEED_ONE");

```

https://github.com/code-423n4/2022-12-Stealth-Project/tree/main/maverick-v1/contracts/models/Factory.sol#L27

https://github.com/code-423n4/2022-12-Stealth-Project/tree/main/maverick-v1/contracts/models/Factory.sol#L35




139: (amountOut >= params.amountOutMinimum, "Too little received");
165: (amountOut >= params.amountOutMinimum, "Too little received");
```

https://github.com/code-423n4/2022-12-Stealth-Project/tree/main/router-v1/contracts/Router.sol#L139

https://github.com/code-423n4/2022-12-Stealth-Project/tree/main/router-v1/contracts/Router.sol#L165

```solidity
188: (amountIn <= params.amountInMaximum, "Too much requested");
197: (amountIn <= params.amountInMaximum, "Too much requested");
```

https://github.com/code-423n4/2022-12-Stealth-Project/tree/main/router-v1/contracts/Router.sol#L188

https://github.com/code-423n4/2022-12-Stealth-Project/tree/main/router-v1/contracts/Router.sol#L197








### <a href="#Summary">[GAS&#x2011;2]</a><a name="GAS&#x2011;2"> Empty Blocks Should Be Removed Or Emit Something

The code should be refactored such that they no longer exist, or the block should do something useful, such as emitting an event or reverting. If the contract is meant to be extended, the contract should be abstract and the function signatures be added without any default implementation. If the block is an empty if-statement block to avoid doing subsequent checks in the else-if/else conditions, the else-if/else conditions should be nested under the negation of the if-statement, because they involve different classes of checks, which may lead to the introduction of errors when the code is later modified (if(x){}else if(y){...}else{...} => if(!x){if(y){...}else{...}})

#### <ins>Proof Of Concept</ins>

```solidity
65: try pool.swap(address(this), amount, tokenAIn, exactOutput, 0, abi.encode(exactOutput)) {} catch Error(string memory _data) {
```

https://github.com/code-423n4/2022-12-Stealth-Project/tree/main/maverick-v1/contracts/models/PoolInspector.sol#L65






### <a href="#Summary">[GAS&#x2011;3]</a><a name="GAS&#x2011;3"> `<x> += <y>` Costs More Gas Than `<x> = <x> + <y>` For State Variables

#### <ins>Proof Of Concept</ins>


```solidity
67: self.state.reserveA += deltaIn;
70: self.state.reserveB += deltaIn;

```

https://github.com/code-423n4/2022-12-Stealth-Project/tree/main/maverick-v1/contracts/libraries/Bin.sol#L67

https://github.com/code-423n4/2022-12-Stealth-Project/tree/main/maverick-v1/contracts/libraries/Bin.sol#L70



```solidity
88: self.state.totalSupply += deltaLpToken128;
89: self.balances[tokenId] += deltaLpToken128;
90: self.state.reserveA += deltaA.toUint128();
91: self.state.reserveB += deltaB.toUint128();

```

https://github.com/code-423n4/2022-12-Stealth-Project/tree/main/maverick-v1/contracts/libraries/Bin.sol#L88-L91

```solidity
114: maxRecursion -= 1;

```

https://github.com/code-423n4/2022-12-Stealth-Project/tree/main/maverick-v1/contracts/libraries/Bin.sol#L114

```solidity
142: self.balances[tokenId] -= deltaLpBalance128;
144: self.state.totalSupply -= deltaLpBalance128;
148: self.state.mergeBinBalance -= deltaLpBalance128;
154: activeBin.state.totalSupply -= deltaLpBalance128;
157: activeBin.balances[tokenId] -= deltaLpBalance128;

```

https://github.com/code-423n4/2022-12-Stealth-Project/tree/main/maverick-v1/contracts/libraries/Bin.sol#L142

https://github.com/code-423n4/2022-12-Stealth-Project/tree/main/maverick-v1/contracts/libraries/Bin.sol#L144

https://github.com/code-423n4/2022-12-Stealth-Project/tree/main/maverick-v1/contracts/libraries/Bin.sol#L148

https://github.com/code-423n4/2022-12-Stealth-Project/tree/main/maverick-v1/contracts/libraries/Bin.sol#L154

https://github.com/code-423n4/2022-12-Stealth-Project/tree/main/maverick-v1/contracts/libraries/Bin.sol#L157



```solidity
54: offset += bitIndex;
66: counter += 1;
68: offset += uint16(uint32(NUMBER_OF_KINDS_32));
70: wordIndex += 1;

```

https://github.com/code-423n4/2022-12-Stealth-Project/tree/main/maverick-v1/contracts/libraries/BinMap.sol#L54

https://github.com/code-423n4/2022-12-Stealth-Project/tree/main/maverick-v1/contracts/libraries/BinMap.sol#L66

https://github.com/code-423n4/2022-12-Stealth-Project/tree/main/maverick-v1/contracts/libraries/BinMap.sol#L68

https://github.com/code-423n4/2022-12-Stealth-Project/tree/main/maverick-v1/contracts/libraries/BinMap.sol#L70



```solidity
97: mapIndex += tack;

```

https://github.com/code-423n4/2022-12-Stealth-Project/tree/main/maverick-v1/contracts/libraries/BinMap.sol#L97

```solidity
18: result += 128;
22: result += 64;
26: result += 32;
30: result += 16;
34: result += 8;
38: result += 4;
42: result += 2;
44: if (x >= 0x2) result += 1;

```

https://github.com/code-423n4/2022-12-Stealth-Project/tree/main/maverick-v1/contracts/libraries/BinMath.sol#L18

https://github.com/code-423n4/2022-12-Stealth-Project/tree/main/maverick-v1/contracts/libraries/BinMath.sol#L22

https://github.com/code-423n4/2022-12-Stealth-Project/tree/main/maverick-v1/contracts/libraries/BinMath.sol#L26

https://github.com/code-423n4/2022-12-Stealth-Project/tree/main/maverick-v1/contracts/libraries/BinMath.sol#L30

https://github.com/code-423n4/2022-12-Stealth-Project/tree/main/maverick-v1/contracts/libraries/BinMath.sol#L34

https://github.com/code-423n4/2022-12-Stealth-Project/tree/main/maverick-v1/contracts/libraries/BinMath.sol#L38

https://github.com/code-423n4/2022-12-Stealth-Project/tree/main/maverick-v1/contracts/libraries/BinMath.sol#L42

https://github.com/code-423n4/2022-12-Stealth-Project/tree/main/maverick-v1/contracts/libraries/BinMath.sol#L44



```solidity
53: result -= 128;
58: result -= 64;
63: result -= 32;
68: result -= 16;
73: result -= 8;
78: result -= 4;
83: result -= 2;
87: if (x & 0x1 > 0) result -= 1;

```

https://github.com/code-423n4/2022-12-Stealth-Project/tree/main/maverick-v1/contracts/libraries/BinMath.sol#L53

https://github.com/code-423n4/2022-12-Stealth-Project/tree/main/maverick-v1/contracts/libraries/BinMath.sol#L58

https://github.com/code-423n4/2022-12-Stealth-Project/tree/main/maverick-v1/contracts/libraries/BinMath.sol#L63

https://github.com/code-423n4/2022-12-Stealth-Project/tree/main/maverick-v1/contracts/libraries/BinMath.sol#L68

https://github.com/code-423n4/2022-12-Stealth-Project/tree/main/maverick-v1/contracts/libraries/BinMath.sol#L73

https://github.com/code-423n4/2022-12-Stealth-Project/tree/main/maverick-v1/contracts/libraries/BinMath.sol#L78

https://github.com/code-423n4/2022-12-Stealth-Project/tree/main/maverick-v1/contracts/libraries/BinMath.sol#L83

https://github.com/code-423n4/2022-12-Stealth-Project/tree/main/maverick-v1/contracts/libraries/BinMath.sol#L87



```solidity
24: self.deltaInBinInternal += delta.deltaInBinInternal;
25: self.deltaInErc += delta.deltaInErc;
26: self.deltaOutErc += delta.deltaOutErc;

```

https://github.com/code-423n4/2022-12-Stealth-Project/tree/main/maverick-v1/contracts/libraries/Delta.sol#L24-L26

```solidity
142: tokenAAmount += temp.deltaA;
143: tokenBAmount += temp.deltaB;
161: binBalanceA += tokenAAmount.toUint128();
162: binBalanceB += tokenBAmount.toUint128();

```

https://github.com/code-423n4/2022-12-Stealth-Project/tree/main/maverick-v1/contracts/models/Pool.sol#L142

https://github.com/code-423n4/2022-12-Stealth-Project/tree/main/maverick-v1/contracts/models/Pool.sol#L143

https://github.com/code-423n4/2022-12-Stealth-Project/tree/main/maverick-v1/contracts/models/Pool.sol#L161

https://github.com/code-423n4/2022-12-Stealth-Project/tree/main/maverick-v1/contracts/models/Pool.sol#L162



```solidity
199: bin.balances[toTokenId] += param.amount;
200: bin.balances[fromTokenId] -= param.amount;

```

https://github.com/code-423n4/2022-12-Stealth-Project/tree/main/maverick-v1/contracts/models/Pool.sol#L199-L200

```solidity
229: tokenAOut += deltaA;
230: tokenBOut += deltaB;

```

https://github.com/code-423n4/2022-12-Stealth-Project/tree/main/maverick-v1/contracts/models/Pool.sol#L229-L230

```solidity
288: binBalanceA += delta.deltaInBinInternal.toUint128();
291: binBalanceB += delta.deltaInBinInternal.toUint128();

```

https://github.com/code-423n4/2022-12-Stealth-Project/tree/main/maverick-v1/contracts/models/Pool.sol#L288

https://github.com/code-423n4/2022-12-Stealth-Project/tree/main/maverick-v1/contracts/models/Pool.sol#L291



```solidity
422: moveData.totalReserveA += bin.state.reserveA;
423: moveData.totalReserveB += bin.state.reserveB;

```

https://github.com/code-423n4/2022-12-Stealth-Project/tree/main/maverick-v1/contracts/models/Pool.sol#L422-L423

```solidity
491: currentState.binCounter += 1;

```

https://github.com/code-423n4/2022-12-Stealth-Project/tree/main/maverick-v1/contracts/models/Pool.sol#L491

```solidity
527: output.currentReserveA += bin.state.reserveA;
528: output.currentReserveB += bin.state.reserveB;

```

https://github.com/code-423n4/2022-12-Stealth-Project/tree/main/maverick-v1/contracts/models/Pool.sol#L527-L528

```solidity
103: reserveA += bin.reserveA;
104: reserveB += bin.reserveB;

```

https://github.com/code-423n4/2022-12-Stealth-Project/tree/main/maverick-v1/contracts/models/PoolInspector.sol#L103-L104





### <a href="#Summary">[GAS&#x2011;4]</a><a name="GAS&#x2011;4"> `++i`/`i++` Should Be `unchecked{++i}`/`unchecked{i++}` When It Is Not Possible For Them To Overflow, As Is The Case When Used In For- And While-loops

The unchecked keyword is new in solidity version 0.8.0, so this only applies to that version or higher, which these instances are. This saves 30-40 gas PER LOOP

#### <ins>Proof Of Concept</ins>


```solidity
127: for (uint256 i; i < params.length; i++) {
```

https://github.com/code-423n4/2022-12-Stealth-Project/tree/main/maverick-v1/contracts/models/Pool.sol#L127

```solidity
180: for (uint256 i; i < binIds.length; i++) {
```

https://github.com/code-423n4/2022-12-Stealth-Project/tree/main/maverick-v1/contracts/models/Pool.sol#L180

```solidity
193: for (uint256 i; i < params.length; i++) {
```

https://github.com/code-423n4/2022-12-Stealth-Project/tree/main/maverick-v1/contracts/models/Pool.sol#L193

```solidity
224: for (uint256 i; i < params.length; i++) {
```

https://github.com/code-423n4/2022-12-Stealth-Project/tree/main/maverick-v1/contracts/models/Pool.sol#L224

```solidity
380: for (uint256 j; j < 2; j++) {
```

https://github.com/code-423n4/2022-12-Stealth-Project/tree/main/maverick-v1/contracts/models/Pool.sol#L380

```solidity
389: for (uint256 i; i <= moveData.binCounter; i++) {
```

https://github.com/code-423n4/2022-12-Stealth-Project/tree/main/maverick-v1/contracts/models/Pool.sol#L389

```solidity
414: for (uint256 i; i < moveData.mergeBinCounter; i++) {
```

https://github.com/code-423n4/2022-12-Stealth-Project/tree/main/maverick-v1/contracts/models/Pool.sol#L414

```solidity
523: for (uint256 i; i < NUMBER_OF_KINDS; i++) {
```

https://github.com/code-423n4/2022-12-Stealth-Project/tree/main/maverick-v1/contracts/models/Pool.sol#L523

```solidity
540: for (uint256 i; i < NUMBER_OF_KINDS; i++) {
```

https://github.com/code-423n4/2022-12-Stealth-Project/tree/main/maverick-v1/contracts/models/Pool.sol#L540

```solidity
653: for (uint256 i; i < swapData.counter; i++) {
```

https://github.com/code-423n4/2022-12-Stealth-Project/tree/main/maverick-v1/contracts/models/Pool.sol#L653

```solidity
30: for (uint128 i = startBinIndex; i < binCounter; i++) {
```

https://github.com/code-423n4/2022-12-Stealth-Project/tree/main/maverick-v1/contracts/models/PoolInspector.sol#L30

```solidity
80: for (uint8 i = 0; i < NUMBER_OF_KINDS; i++) {
```

https://github.com/code-423n4/2022-12-Stealth-Project/tree/main/maverick-v1/contracts/models/PoolInspector.sol#L80

```solidity
101: for (uint256 i; i < bins.length; i++) {
```

https://github.com/code-423n4/2022-12-Stealth-Project/tree/main/maverick-v1/contracts/models/PoolInspector.sol#L101

```solidity
13: for (uint256 i = 0; i < data.length; i++) {
```

https://github.com/code-423n4/2022-12-Stealth-Project/tree/main/router-v1/contracts/libraries/Multicall.sol#L13





### <a href="#Summary">[GAS&#x2011;5]</a><a name="GAS&#x2011;5"> `require()`/`revert()` Strings Longer Than 32 Bytes Cost Extra Gas

#### <ins>Proof Of Concept</ins>


```solidity
35: require(_protocolFeeRatio <= ONE_3_DECIMAL_SCALE, "Factory:PROTOCOL_FEE_CANNOT_EXCEED_ONE");
```

https://github.com/code-423n4/2022-12-Stealth-Project/tree/main/maverick-v1/contracts/models/Factory.sol#L35

```solidity
59: require(_tokenA < _tokenB, "Factory:TOKENS_MUST_BE_SORTED_BY_ADDRESS");
```

https://github.com/code-423n4/2022-12-Stealth-Project/tree/main/maverick-v1/contracts/models/Factory.sol#L59

```solidity
61: require(_tickSpacing > 0, "Factory:TICK_SPACING_OUT_OF_BOUNDS");
```

https://github.com/code-423n4/2022-12-Stealth-Project/tree/main/maverick-v1/contracts/models/Factory.sol#L61

```solidity
62: require(_lookback >= 3600 && _lookback <= uint16(type(int16).max), "Factory:LOOKBACK_OUT_OF_BOUNDS");
```

https://github.com/code-423n4/2022-12-Stealth-Project/tree/main/maverick-v1/contracts/models/Factory.sol#L62

```solidity
101: require(factory.isFactoryPool(IPool(msg.sender)), "Must call from a Factory Pool");
```

https://github.com/code-423n4/2022-12-Stealth-Project/tree/main/router-v1/contracts/Router.sol#L101

```solidity
177: require(amountOutReceived == amountOut, "Requested amount not available");
```

https://github.com/code-423n4/2022-12-Stealth-Project/tree/main/router-v1/contracts/Router.sol#L177







### <a href="#Summary">[GAS&#x2011;6]</a><a name="GAS&#x2011;6"> Splitting `require()` statements that use `&&` saves gas

Instead of using operator `&&` on a single `require`. Using a two `require` can save more gas.

i.e.
for `require(version == 1 && _bytecodeHash[1] == bytes1(0), "zf");` use:

```
	require(version == 1);
	require(_bytecodeHash[1] == bytes1(0));
```

#### <ins>Proof Of Concept</ins>

```solidity
41: require(msg.sender == owner && _owner != address(0), "Factory:NOT_ALLOWED");
```

https://github.com/code-423n4/2022-12-Stealth-Project/tree/main/maverick-v1/contracts/models/Factory.sol#L41

```solidity
60: require(_fee > 0 && _fee < 1e18, "Factory:FEE_OUT_OF_BOUNDS");
```

https://github.com/code-423n4/2022-12-Stealth-Project/tree/main/maverick-v1/contracts/models/Factory.sol#L60

```solidity
62: require(_lookback >= 3600 && _lookback <= uint16(type(int16).max), "Factory:LOOKBACK_OUT_OF_BOUNDS");
```

https://github.com/code-423n4/2022-12-Stealth-Project/tree/main/maverick-v1/contracts/models/Factory.sol#L62

```solidity
93: require((currentState.status & LOCKED == 0) && (allowInEmergency || (currentState.status & EMERGENCY == 0)), "E");
```

https://github.com/code-423n4/2022-12-Stealth-Project/tree/main/maverick-v1/contracts/models/Pool.sol#L93

```solidity
168: require(previousABalance + tokenAAmount <= _tokenABalance() && previousBBalance + tokenBAmount <= _tokenBBalance(), "A");
```

https://github.com/code-423n4/2022-12-Stealth-Project/tree/main/maverick-v1/contracts/models/Pool.sol#L168

```solidity
100: require(amountToPay > 0 && amountOut > 0, "In or Out Amount is Zero");
```

https://github.com/code-423n4/2022-12-Stealth-Project/tree/main/router-v1/contracts/Router.sol#L100

```solidity
234: require(tokenAAmount >= minTokenAAmount && tokenBAmount >= minTokenBAmount, "Too little added");
```

https://github.com/code-423n4/2022-12-Stealth-Project/tree/main/router-v1/contracts/Router.sol#L234

```solidity
262: require(activeTick >= minActiveTick && activeTick <= maxActiveTick, "activeTick not in range");
```

https://github.com/code-423n4/2022-12-Stealth-Project/tree/main/router-v1/contracts/Router.sol#L262

```solidity
309: require(tokenAAmount >= minTokenAAmount && tokenBAmount >= minTokenBAmount, "Too little removed");
```

https://github.com/code-423n4/2022-12-Stealth-Project/tree/main/router-v1/contracts/Router.sol#L309

```solidity
15: require(success && (data.length == 0 || abi.decode(data, (bool))), "STF");
```

https://github.com/code-423n4/2022-12-Stealth-Project/tree/main/router-v1/contracts/libraries/TransferHelper.sol#L15

```solidity
25: require(success && (data.length == 0 || abi.decode(data, (bool))), "ST");
```

https://github.com/code-423n4/2022-12-Stealth-Project/tree/main/router-v1/contracts/libraries/TransferHelper.sol#L25

```solidity
35: require(success && (data.length == 0 || abi.decode(data, (bool))), "SA");
```

https://github.com/code-423n4/2022-12-Stealth-Project/tree/main/router-v1/contracts/libraries/TransferHelper.sol#L35





### <a href="#Summary">[GAS&#x2011;7]</a><a name="GAS&#x2011;7"> `abi.encode()` is less efficient than `abi.encodepacked()`

See for more information: https://github.com/ConnorBlockchain/Solidity-Encode-Gas-Comparison 

#### <ins>Proof Of Concept</ins>


```solidity
24: pool = new Pool{salt: keccak256(abi.encode(_fee, _tickSpacing, _lookback, _tokenA, _tokenB))}(
```

https://github.com/code-423n4/2022-12-Stealth-Project/tree/main/maverick-v1/contracts/libraries/Deployer.sol#L24

```solidity
58: revert(string(abi.encode(amountIn)));
```

https://github.com/code-423n4/2022-12-Stealth-Project/tree/main/maverick-v1/contracts/models/PoolInspector.sol#L58

```solidity
60: revert(string(abi.encode(amountOut)));
```

https://github.com/code-423n4/2022-12-Stealth-Project/tree/main/maverick-v1/contracts/models/PoolInspector.sol#L60

```solidity
65: try pool.swap(address(this), amount, tokenAIn, exactOutput, 0, abi.encode(exactOutput)) {} catch Error(string memory _data) {
```

https://github.com/code-423n4/2022-12-Stealth-Project/tree/main/maverick-v1/contracts/models/PoolInspector.sol#L65

```solidity
128: (, amountOut) = pool.swap(recipient, amountIn, tokenAIn, false, sqrtPriceLimitD18, abi.encode(data));
```

https://github.com/code-423n4/2022-12-Stealth-Project/tree/main/router-v1/contracts/Router.sol#L128

```solidity
176: (amountIn, amountOutReceived) = pool.swap(recipient, amountOut, tokenAIn, true, 0, abi.encode(data));
```

https://github.com/code-423n4/2022-12-Stealth-Project/tree/main/router-v1/contracts/Router.sol#L176

```solidity
232: (tokenAAmount, tokenBAmount, binDeltas) = pool.addLiquidity(tokenId, params, abi.encode(data));
```

https://github.com/code-423n4/2022-12-Stealth-Project/tree/main/router-v1/contracts/Router.sol#L232







### <a href="#Summary">[GAS&#x2011;8]</a><a name="GAS&#x2011;8"> Public Functions To External

The following functions could be set external to save gas and improve code quality.
External call cost is less expensive than of public functions.

#### <ins>Proof Of Concept</ins>


```solidity
function getBinDepth(IPool pool, uint128 binId) public view returns (uint256 depth) {
```

https://github.com/code-423n4/2022-12-Stealth-Project/tree/main/maverick-v1/contracts/models/PoolInspector.sol#L45

```solidity
function supportsInterface(bytes4 interfaceId) public view override(ERC721, ERC721Enumerable, IERC165) returns (bool) {
```

https://github.com/code-423n4/2022-12-Stealth-Project/tree/main/maverick-v1/contracts/models/Position.sol#L38

```solidity
function tokenURI(uint256 tokenId) public view virtual override returns (string memory) {
```

https://github.com/code-423n4/2022-12-Stealth-Project/tree/main/maverick-v1/contracts/models/Position.sol#L42

```solidity
function unwrapWETH9(uint256 amountMinimum, address recipient) public payable override {
```

https://github.com/code-423n4/2022-12-Stealth-Project/tree/main/router-v1/contracts/Router.sol#L59

```solidity
function sweepToken(IERC20 token, uint256 amountMinimum, address recipient) public payable {
```

https://github.com/code-423n4/2022-12-Stealth-Project/tree/main/router-v1/contracts/Router.sol#L70

```solidity
function multicall(bytes[] calldata data) public payable override returns (bytes[] memory results) {
```

https://github.com/code-423n4/2022-12-Stealth-Project/tree/main/router-v1/contracts/libraries/Multicall.sol#L11

```solidity
function selfPermit(address token, uint256 value, uint256 deadline, uint8 v, bytes32 r, bytes32 s) public payable override {
```

https://github.com/code-423n4/2022-12-Stealth-Project/tree/main/router-v1/contracts/libraries/SelfPermit.sol#L16

```solidity
function selfPermitAllowed(address token, uint256 nonce, uint256 expiry, uint8 v, bytes32 r, bytes32 s) public payable override {
```

https://github.com/code-423n4/2022-12-Stealth-Project/tree/main/router-v1/contracts/libraries/SelfPermit.sol#L26






### <a href="#Summary">[GAS&#x2011;9]</a><a name="GAS&#x2011;9"> Optimize names to save gas

Contracts most called functions could simply save gas by function ordering via Method ID. Calling a function at runtime will be cheaper if the function is positioned earlier in the order (has a relatively lower Method ID) because 22 gas are added to the cost of a function for every position that came before it. The caller can save on gas if you prioritize most called functions. 

See more <a href="https://medium.com/joyso/solidity-how-does-function-name-affect-gas-consumption-in-smart-contract-47d270d8ac92">here</a>

#### <ins>Proof Of Concept</ins>

```solidity
File: \2022-12-Stealth-Project\maverick-v1\contracts\models\Factory.sol
```

https://github.com/code-423n4/2022-12-Stealth-Project/tree/main/maverick-v1/contracts/models/Factory.sol

```solidity
File: \2022-12-Stealth-Project\maverick-v1\contracts\models\Pool.sol
```

https://github.com/code-423n4/2022-12-Stealth-Project/tree/main/maverick-v1/contracts/models/Pool.sol

```solidity
File: \2022-12-Stealth-Project\maverick-v1\contracts\models\PoolInspector.sol
```

https://github.com/code-423n4/2022-12-Stealth-Project/tree/main/maverick-v1/contracts/models/PoolInspector.sol

```solidity
File: \2022-12-Stealth-Project\maverick-v1\contracts\models\Position.sol
```

https://github.com/code-423n4/2022-12-Stealth-Project/tree/main/maverick-v1/contracts/models/Position.sol

```solidity
File: \2022-12-Stealth-Project\maverick-v1\contracts\models\PositionMetadata.sol
```

https://github.com/code-423n4/2022-12-Stealth-Project/tree/main/maverick-v1/contracts/models/PositionMetadata.sol

```solidity
File: \2022-12-Stealth-Project\router-v1\contracts\Router.sol
```

https://github.com/code-423n4/2022-12-Stealth-Project/tree/main/router-v1/contracts/Router.sol

```solidity
File: \2022-12-Stealth-Project\router-v1\contracts\libraries\Deadline.sol
```

https://github.com/code-423n4/2022-12-Stealth-Project/tree/main/router-v1/contracts/libraries/Deadline.sol

```solidity
File: \2022-12-Stealth-Project\router-v1\contracts\libraries\Multicall.sol
```

https://github.com/code-423n4/2022-12-Stealth-Project/tree/main/router-v1/contracts/libraries/Multicall.sol

```solidity
File: \2022-12-Stealth-Project\router-v1\contracts\libraries\SelfPermit.sol
```

https://github.com/code-423n4/2022-12-Stealth-Project/tree/main/router-v1/contracts/libraries/SelfPermit.sol



#### <ins>Recommended Mitigation Steps</ins>
Find a lower method ID name for the most called functions for example Call() vs. Call1() is cheaper by 22 gas
For example, the function IDs in the Gauge.sol contract will be the most used; A lower method ID may be given.




### <a href="#Summary">[GAS&#x2011;10]</a><a name="GAS&#x2011;10"> Do not calculate constants

Due to how constant variables are implemented (replacements at compile-time), an expression assigned to a constant variable is recomputed each time that the variable is used, which wastes some gas.

#### <ins>Proof Of Concept</ins>

```solidity
18: uint256 private constant NEXT_OFFSET = ADDR_SIZE + ADDR_SIZE;
```

https://github.com/code-423n4/2022-12-Stealth-Project/tree/main/router-v1/contracts/libraries/Path.sol#L18

```solidity
20: uint256 private constant POP_OFFSET = NEXT_OFFSET + ADDR_SIZE;
```

https://github.com/code-423n4/2022-12-Stealth-Project/tree/main/router-v1/contracts/libraries/Path.sol#L20

```solidity
22: uint256 private constant MULTIPLE_POOLS_MIN_LENGTH = POP_OFFSET + NEXT_OFFSET;
```

https://github.com/code-423n4/2022-12-Stealth-Project/tree/main/router-v1/contracts/libraries/Path.sol#L22






### <a href="#Summary">[GAS&#x2011;11]</a><a name="GAS&#x2011;11"> `internal` functions only called once can be inlined to save gas

#### <ins>Proof Of Concept</ins>

```solidity
320: function claimProtocolFees

```

https://github.com/code-423n4/2022-12-Stealth-Project/tree/main/maverick-v1/contracts/models/Pool.sol#L320

```solidity
448: function _moveBins

```

https://github.com/code-423n4/2022-12-Stealth-Project/tree/main/maverick-v1/contracts/models/Pool.sol#L448

```solidity
486: function _getOrCreateBin

```

https://github.com/code-423n4/2022-12-Stealth-Project/tree/main/maverick-v1/contracts/models/Pool.sol#L486

```solidity
516: function _currentTickLiquidity

```

https://github.com/code-423n4/2022-12-Stealth-Project/tree/main/maverick-v1/contracts/models/Pool.sol#L516

```solidity
537: function _firstKindAtTickLiquidity

```

https://github.com/code-423n4/2022-12-Stealth-Project/tree/main/maverick-v1/contracts/models/Pool.sol#L537

```solidity
553: function _computeSwapExactIn

```

https://github.com/code-423n4/2022-12-Stealth-Project/tree/main/maverick-v1/contracts/models/Pool.sol#L553

```solidity
597: function _computeSwapExactOut

```

https://github.com/code-423n4/2022-12-Stealth-Project/tree/main/maverick-v1/contracts/models/Pool.sol#L597

```solidity
614: function _swapTick

```

https://github.com/code-423n4/2022-12-Stealth-Project/tree/main/maverick-v1/contracts/models/Pool.sol#L614

```solidity
77: function _getBinsAtTick

```

https://github.com/code-423n4/2022-12-Stealth-Project/tree/main/maverick-v1/contracts/models/PoolInspector.sol#L77

```solidity
95: function _currentTickLiquidity

```

https://github.com/code-423n4/2022-12-Stealth-Project/tree/main/maverick-v1/contracts/models/PoolInspector.sol#L95



### <a href="#Summary">[GAS&#x2011;12]</a><a name="GAS&#x2011;12"> Setting the `constructor` to `payable`

Saves ~13 gas per instance

#### <ins>Proof Of Concept</ins>

```solidity
26: constructor(uint16 _protocolFeeRatio, IPosition _position)
```

https://github.com/code-423n4/2022-12-Stealth-Project/tree/main/maverick-v1/contracts/models/Factory.sol#L26

```solidity
60: constructor(
        uint256 _fee,
        uint256 _tickSpacing,
        int32 _activeTick,
        uint16 _lookback,
        uint16 _protocolFeeRatio,
        IERC20 _tokenA,
        IERC20 _tokenB,
        uint256 _tokenAScale,
        uint256 _tokenBScale,
        IPosition _position
    )
```

https://github.com/code-423n4/2022-12-Stealth-Project/tree/main/maverick-v1/contracts/models/Pool.sol#L60

```solidity
18: constructor(IPositionMetadata _metadata) ERC721("Maverick Position NFT", "MPN")
```

https://github.com/code-423n4/2022-12-Stealth-Project/tree/main/maverick-v1/contracts/models/Position.sol#L18

```solidity
13: constructor(string memory _baseURI)
```

https://github.com/code-423n4/2022-12-Stealth-Project/tree/main/maverick-v1/contracts/models/PositionMetadata.sol#L13

```solidity
48: constructor(IFactory _factory, IPosition _position, IWETH9 _WETH9)
```

https://github.com/code-423n4/2022-12-Stealth-Project/tree/main/router-v1/contracts/Router.sol#L48





### <a href="#Summary">[GAS&#x2011;13]</a><a name="GAS&#x2011;13"> Functions guaranteed to revert when called by normal users can be marked `payable`

If a function modifier or require such as onlyOwner/onlyX is used, the function will revert if a normal user tries to pay the function. Marking the function as payable will lower the gas cost for legitimate callers because the compiler will not include checks for whether a payment was provided. The extra opcodes avoided are CALLVALUE(2), DUP1(3), ISZERO(3), PUSH2(3), JUMPI(10), PUSH1(3), DUP1(3), REVERT(0), JUMPDEST(1), POP(2) which costs an average of about 21 gas per call to the function, in addition to the extra deployment cost.

#### <ins>Proof Of Concept</ins>

```solidity
23: function setMetadata(IPositionMetadata _metadata) external onlyOwner {

```

https://github.com/code-423n4/2022-12-Stealth-Project/tree/main/maverick-v1/contracts/models/Position.sol#L23

```solidity
17: function setBaseURI(string memory _baseURI) external onlyOwner {

```

https://github.com/code-423n4/2022-12-Stealth-Project/tree/main/maverick-v1/contracts/models/PositionMetadata.sol#L17



#### <ins>Recommended Mitigation Steps</ins>
Functions guaranteed to revert when called by normal users can be marked payable.





