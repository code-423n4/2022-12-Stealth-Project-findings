# Gas Optimizations

|         | Issue                                                                            | Instances |
| ------- | :------------------------------------------------------------------------------- | :-------: |
| [G-001] | Structs can be packed into fewer storage slots                                   |     2     |
| [G-002] | x += y or x -= y costs more gas than x = x + y or x = x - y for state variables  |    26     |
| [G-003] | Splitting require() statements that use `&&` saves gas                           |    12     |
| [G-004] | Functions guaranteed to revert when called by normal users can be marked payable |     2     |
| [G-005] | internal functions only called once can be inlined to save gas                   |     9     |
| [G-006] | Usage of `uints/ints` smaller than 32 bytes (256 bits) incurs overhead           |     5     |
| [G-007] | Use named returns for local variables where it is possible.                      |     1     |

## [G-001] Structs can be packed into fewer storage slots

### Impact

Each slot saved can avoid an extra Gsset (20000 gas) for the first setting of the struct. Subsequent reads as well as writes have smaller gas savings

### Findings

Total:2

[maverick-v1/contracts/libraries/Delta.sol#L5-20](https://github.com/code-423n4/2022-12-Stealth-Project/blob/main//maverick-v1/contracts/libraries/Delta.sol#L5-20)

```solidity
5:    struct Instance {
6:            uint256 deltaInBinInternal;
7:            uint256 deltaInErc;
8:            uint256 deltaOutErc;
9:            uint256 excess;
10:            bool tokenAIn;
11:            uint256 endSqrtPrice;
12:            bool exactOutput;
13:            bool swappedToMaxPrice;
14:            bool skipCombine;
15:            bool decrementTick;
16:            uint256 sqrtPriceLimit;
17:            uint256 sqrtLowerTickPrice;
18:            uint256 sqrtUpperTickPrice;
19:            uint256 sqrtPrice;
20:        }
```

[maverick-v1/contracts/models/Pool.sol#L363-375](https://github.com/code-423n4/2022-12-Stealth-Project/blob/main//maverick-v1/contracts/models/Pool.sol#L363-375)

```solidity
363:    struct MoveData {
364:            int32 tickLimit;
365:            uint8[2] kinds;
366:            int32 shiftAmount;
367:            BinMap.Active[] activeList;
368:            uint128 firstBinId;
369:            uint256 mergeBinBalance;
370:            uint128 totalReserveA;
371:            uint128 totalReserveB;
372:            uint256 binCounter;
373:            uint256 mergeBinCounter;
374:            uint128[] mergeBins;
375:        }
```

## [G-002] x += y or x -= y costs more gas than x = x + y or x = x - y for state variables

### Impact

Using the addition operator instead of plus-equals saves 113 gas. Usually does not work with struct and mappings.

### Findings

Total:26

[maverick-v1/contracts/libraries/Bin.sol#L114](https://github.com/code-423n4/2022-12-Stealth-Project/blob/main//maverick-v1/contracts/libraries/Bin.sol#L114)

```solidity
114:    maxRecursion -= 1;
```

[maverick-v1/contracts/libraries/BinMap.sol#L54](https://github.com/code-423n4/2022-12-Stealth-Project/blob/main//maverick-v1/contracts/libraries/BinMap.sol#L54)

```solidity
54:    offset += bitIndex;
```

[maverick-v1/contracts/libraries/BinMap.sol#L97](https://github.com/code-423n4/2022-12-Stealth-Project/blob/main//maverick-v1/contracts/libraries/BinMap.sol#L97)

```solidity
97:    mapIndex += tack;
```

[maverick-v1/contracts/libraries/BinMap.sol#L70](https://github.com/code-423n4/2022-12-Stealth-Project/blob/main//maverick-v1/contracts/libraries/BinMap.sol#L70)

```solidity
70:    wordIndex += 1;
```

[maverick-v1/contracts/libraries/BinMap.sol#L66](https://github.com/code-423n4/2022-12-Stealth-Project/blob/main//maverick-v1/contracts/libraries/BinMap.sol#L66)

```solidity
66:    counter += 1;
```

[maverick-v1/contracts/libraries/BinMap.sol#L68](https://github.com/code-423n4/2022-12-Stealth-Project/blob/main//maverick-v1/contracts/libraries/BinMap.sol#L68)

```solidity
68:    offset += uint16(uint32(NUMBER_OF_KINDS_32));
```

[maverick-v1/contracts/libraries/BinMath.sol#L63](https://github.com/code-423n4/2022-12-Stealth-Project/blob/main//maverick-v1/contracts/libraries/BinMath.sol#L63)

```solidity
63:    result -= 32;
```

[maverick-v1/contracts/libraries/BinMath.sol#L53](https://github.com/code-423n4/2022-12-Stealth-Project/blob/main//maverick-v1/contracts/libraries/BinMath.sol#L53)

```solidity
53:    result -= 128;
```

[maverick-v1/contracts/libraries/BinMath.sol#L83](https://github.com/code-423n4/2022-12-Stealth-Project/blob/main//maverick-v1/contracts/libraries/BinMath.sol#L83)

```solidity
83:    result -= 2;
```

[maverick-v1/contracts/libraries/BinMath.sol#L22](https://github.com/code-423n4/2022-12-Stealth-Project/blob/main//maverick-v1/contracts/libraries/BinMath.sol#L22)

```solidity
22:    result += 64;
```

[maverick-v1/contracts/libraries/BinMath.sol#L68](https://github.com/code-423n4/2022-12-Stealth-Project/blob/main//maverick-v1/contracts/libraries/BinMath.sol#L68)

```solidity
68:    result -= 16;
```

[maverick-v1/contracts/libraries/BinMath.sol#L78](https://github.com/code-423n4/2022-12-Stealth-Project/blob/main//maverick-v1/contracts/libraries/BinMath.sol#L78)

```solidity
78:    result -= 4;
```

[maverick-v1/contracts/libraries/BinMath.sol#L26](https://github.com/code-423n4/2022-12-Stealth-Project/blob/main//maverick-v1/contracts/libraries/BinMath.sol#L26)

```solidity
26:    result += 32;
```

[maverick-v1/contracts/libraries/BinMath.sol#L34](https://github.com/code-423n4/2022-12-Stealth-Project/blob/main//maverick-v1/contracts/libraries/BinMath.sol#L34)

```solidity
34:    result += 8;
```

[maverick-v1/contracts/libraries/BinMath.sol#L18](https://github.com/code-423n4/2022-12-Stealth-Project/blob/main//maverick-v1/contracts/libraries/BinMath.sol#L18)

```solidity
18:    result += 128;
```

[maverick-v1/contracts/libraries/BinMath.sol#L42](https://github.com/code-423n4/2022-12-Stealth-Project/blob/main//maverick-v1/contracts/libraries/BinMath.sol#L42)

```solidity
42:    result += 2;
```

[maverick-v1/contracts/libraries/BinMath.sol#L30](https://github.com/code-423n4/2022-12-Stealth-Project/blob/main//maverick-v1/contracts/libraries/BinMath.sol#L30)

```solidity
30:    result += 16;
```

[maverick-v1/contracts/libraries/BinMath.sol#L73](https://github.com/code-423n4/2022-12-Stealth-Project/blob/main//maverick-v1/contracts/libraries/BinMath.sol#L73)

```solidity
73:    result -= 8;
```

[maverick-v1/contracts/libraries/BinMath.sol#L87](https://github.com/code-423n4/2022-12-Stealth-Project/blob/main//maverick-v1/contracts/libraries/BinMath.sol#L87)

```solidity
87:    if (x & 0x1 > 0) result -= 1;
```

[maverick-v1/contracts/libraries/BinMath.sol#L38](https://github.com/code-423n4/2022-12-Stealth-Project/blob/main//maverick-v1/contracts/libraries/BinMath.sol#L38)

```solidity
38:    result += 4;
```

[maverick-v1/contracts/libraries/BinMath.sol#L44](https://github.com/code-423n4/2022-12-Stealth-Project/blob/main//maverick-v1/contracts/libraries/BinMath.sol#L44)

```solidity
44:    if (x >= 0x2) result += 1;
```

[maverick-v1/contracts/libraries/BinMath.sol#L58](https://github.com/code-423n4/2022-12-Stealth-Project/blob/main//maverick-v1/contracts/libraries/BinMath.sol#L58)

```solidity
58:    result -= 64;
```

[maverick-v1/contracts/models/Pool.sol#L229](https://github.com/code-423n4/2022-12-Stealth-Project/blob/main//maverick-v1/contracts/models/Pool.sol#L229)

```solidity
229:    tokenAOut += deltaA;
```

[maverick-v1/contracts/models/Pool.sol#L230](https://github.com/code-423n4/2022-12-Stealth-Project/blob/main//maverick-v1/contracts/models/Pool.sol#L230)

```solidity
230:    tokenBOut += deltaB;
```

[maverick-v1/contracts/models/Pool.sol#L161](https://github.com/code-423n4/2022-12-Stealth-Project/blob/main//maverick-v1/contracts/models/Pool.sol#L161)

```solidity
161:    binBalanceA += tokenAAmount.toUint128();
```

[maverick-v1/contracts/models/Pool.sol#L162](https://github.com/code-423n4/2022-12-Stealth-Project/blob/main//maverick-v1/contracts/models/Pool.sol#L162)

```solidity
162:    binBalanceB += tokenBAmount.toUint128();
```

## [G-003] Splitting require() statements that use `&&` saves gas

### Impact

See [this issue](https://github.com/code-423n4/2022-01-xdefi-findings/issues/128) which describes the fact that there is a larger deployment gas cost, but with enough runtime calls, the change ends up being cheaper by 3 gas.

### Findings

Total:12

[router-v1/contracts/Router.sol#L309](https://github.com/code-423n4/2022-12-Stealth-Project/blob/main//router-v1/contracts/Router.sol#L309)

```solidity
309:    require(tokenAAmount >= minTokenAAmount && tokenBAmount >= minTokenBAmount, "Too little removed");
```

[router-v1/contracts/Router.sol#L234](https://github.com/code-423n4/2022-12-Stealth-Project/blob/main//router-v1/contracts/Router.sol#L234)

```solidity
234:    require(tokenAAmount >= minTokenAAmount && tokenBAmount >= minTokenBAmount, "Too little added");
```

[router-v1/contracts/Router.sol#L100](https://github.com/code-423n4/2022-12-Stealth-Project/blob/main//router-v1/contracts/Router.sol#L100)

```solidity
100:    require(amountToPay > 0 && amountOut > 0, "In or Out Amount is Zero");
```

[router-v1/contracts/Router.sol#L262](https://github.com/code-423n4/2022-12-Stealth-Project/blob/main//router-v1/contracts/Router.sol#L262)

```solidity
262:    require(activeTick >= minActiveTick && activeTick <= maxActiveTick, "activeTick not in range");
```

[router-v1/contracts/libraries/TransferHelper.sol#L25](https://github.com/code-423n4/2022-12-Stealth-Project/blob/main//router-v1/contracts/libraries/TransferHelper.sol#L25)

```solidity
25:    require(success && (data.length == 0 || abi.decode(data, (bool))), "ST");
```

[router-v1/contracts/libraries/TransferHelper.sol#L35](https://github.com/code-423n4/2022-12-Stealth-Project/blob/main//router-v1/contracts/libraries/TransferHelper.sol#L35)

```solidity
35:    require(success && (data.length == 0 || abi.decode(data, (bool))), "SA");
```

[router-v1/contracts/libraries/TransferHelper.sol#L15](https://github.com/code-423n4/2022-12-Stealth-Project/blob/main//router-v1/contracts/libraries/TransferHelper.sol#L15)

```solidity
15:    require(success && (data.length == 0 || abi.decode(data, (bool))), "STF");
```

[maverick-v1/contracts/models/Factory.sol#L41](https://github.com/code-423n4/2022-12-Stealth-Project/blob/main//maverick-v1/contracts/models/Factory.sol#L41)

```solidity
41:    require(msg.sender == owner && _owner != address(0), "Factory:NOT_ALLOWED");
```

[maverick-v1/contracts/models/Factory.sol#L60](https://github.com/code-423n4/2022-12-Stealth-Project/blob/main//maverick-v1/contracts/models/Factory.sol#L60)

```solidity
60:    require(_fee > 0 && _fee < 1e18, "Factory:FEE_OUT_OF_BOUNDS");
```

[maverick-v1/contracts/models/Factory.sol#L62](https://github.com/code-423n4/2022-12-Stealth-Project/blob/main//maverick-v1/contracts/models/Factory.sol#L62)

```solidity
62:    require(_lookback >= 3600 && _lookback <= uint16(type(int16).max), "Factory:LOOKBACK_OUT_OF_BOUNDS");
```

[maverick-v1/contracts/models/Pool.sol#L93](https://github.com/code-423n4/2022-12-Stealth-Project/blob/main//maverick-v1/contracts/models/Pool.sol#L93)

```solidity
93:    require((currentState.status & LOCKED == 0) && (allowInEmergency || (currentState.status & EMERGENCY == 0)), "E");
```

[maverick-v1/contracts/models/Pool.sol#L168](https://github.com/code-423n4/2022-12-Stealth-Project/blob/main//maverick-v1/contracts/models/Pool.sol#L168)

```solidity
168:    require(previousABalance + tokenAAmount <= _tokenABalance() && previousBBalance + tokenBAmount <= _tokenBBalance(), "A");
```

## [G-004] Functions guaranteed to revert when called by normal users can be marked payable

### Impact

If a function modifier such as `onlyOwner` is used, the function will revert if a normal user tries to pay the function. Marking the function as `payable` will lower the gas cost for legitimate callers because the compiler will not include checks for whether a payment was provided.
The extra opcodes avoided are `CALLVALUE(2)`,`DUP1(3)`,`ISZERO(3)`,`PUSH2(3)`,`JUMPI(10)`,`PUSH1(3)`,`DUP1(3)`,`REVERT(0)`,`JUMPDEST(1)`,`POP(2)`, which costs an average of about 21 gas per call to the function, in addition to the extra deployment cost.

### Findings

Total:2

[maverick-v1/contracts/models/Position.sol#L23](https://github.com/code-423n4/2022-12-Stealth-Project/blob/main//maverick-v1/contracts/models/Position.sol#L23)

```solidity
23:    function setMetadata(IPositionMetadata _metadata) external onlyOwner {
```

[maverick-v1/contracts/models/PositionMetadata.sol#L17](https://github.com/code-423n4/2022-12-Stealth-Project/blob/main//maverick-v1/contracts/models/PositionMetadata.sol#L17)

```solidity
17:    function setBaseURI(string memory _baseURI) external onlyOwner {
```

## [G-005] internal functions only called once can be inlined to save gas

### Impact

Not inlining costs 20 to 40 gas because of two extra `JUMP` instructions and additional stack operations needed for function calls.

### Findings

Total:9

[maverick-v1/contracts/models/Position.sol#L34](https://github.com/code-423n4/2022-12-Stealth-Project/blob/main//maverick-v1/contracts/models/Position.sol#L34)

```solidity
34:    function _beforeTokenTransfer(address from, address to, uint256 tokenId) internal override(ERC721, ERC721Enumerable) {
```

[maverick-v1/contracts/models/PoolInspector.sol#L95](https://github.com/code-423n4/2022-12-Stealth-Project/blob/main//maverick-v1/contracts/models/PoolInspector.sol#L95)

```solidity
95:    function _currentTickLiquidity(IPool pool) internal view returns (uint256 sqrtPrice, uint256 liquidity, uint256 reserveA, uint256 reserveB) {
```

[maverick-v1/contracts/models/PoolInspector.sol#L77](https://github.com/code-423n4/2022-12-Stealth-Project/blob/main//maverick-v1/contracts/models/PoolInspector.sol#L77)

```solidity
77:    function _getBinsAtTick(IPool pool, int32 tick) internal view returns (IPool.BinState[] memory bins) {
```

[maverick-v1/contracts/models/Pool.sol#L486](https://github.com/code-423n4/2022-12-Stealth-Project/blob/main//maverick-v1/contracts/models/Pool.sol#L486)

```solidity
486:    function _getOrCreateBin(State memory currentState, uint8 kind, int32 pos, bool isDelta) internal returns (uint128 binId, Bin.Instance storage bin) {
```

[maverick-v1/contracts/models/Pool.sol#L537](https://github.com/code-423n4/2022-12-Stealth-Project/blob/main//maverick-v1/contracts/models/Pool.sol#L537)

```solidity
537:    function _firstKindAtTickLiquidity(int32 activeTick) internal view returns (uint256 currentReserveA, uint256 currentReserveB) {
```

[maverick-v1/contracts/models/Pool.sol#L448](https://github.com/code-423n4/2022-12-Stealth-Project/blob/main//maverick-v1/contracts/models/Pool.sol#L448)

```solidity
448:    function _moveBins(int32 activeTick, int32 startingTickInitial, int32 lastTwap) internal {
```

[maverick-v1/contracts/models/Pool.sol#L597](https://github.com/code-423n4/2022-12-Stealth-Project/blob/main//maverick-v1/contracts/models/Pool.sol#L597)

```solidity
597:    function _computeSwapExactOut(uint256 sqrtPrice, uint256 liquidity, uint256 reserveA, uint256 reserveB, uint256 amountOut, bool tokenAIn) internal view returns (Delta.Instance memory delta) {
```

[maverick-v1/contracts/models/Pool.sol#L516](https://github.com/code-423n4/2022-12-Stealth-Project/blob/main//maverick-v1/contracts/models/Pool.sol#L516)

```solidity
516:    function _currentTickLiquidity(int32 tick) internal view returns (uint256 sqrtLowerTickPrice, uint256 sqrtUpperTickPrice, uint256 sqrtPrice, uint128[] memory binIds, SwapData memory output) {
```

[maverick-v1/contracts/models/Pool.sol#L614](https://github.com/code-423n4/2022-12-Stealth-Project/blob/main//maverick-v1/contracts/models/Pool.sol#L614)

```solidity
614:    function _swapTick(State memory currentState, Delta.Instance memory delta) internal returns (Delta.Instance memory newDelta) {
```

## [G-006] Usage of `uints/ints` smaller than 32 bytes (256 bits) incurs overhead

### Impact

> When using elements that are smaller than 32 bytes, your contract’s gas usage may be higher. This is because the EVM operates on 32 bytes at a time. Therefore, if the element is smaller than that, the EVM must use more operations in order to reduce the size of the element from 32 bytes to the desired size.

[https://docs.soliditylang.org/en/v0.8.11/internals/layout_in_storage.html](https://docs.soliditylang.org/en/v0.8.11/internals/layout_in_storage.html) Each operation involving a uint8 costs an extra 22-28 gas (depending on whether the other operand is also a variable of type uint8) as compared to ones involving uint256, due to the compiler having to clear the higher bits of the memory word before operating on the uint8, as well as the associated stack operations of doing so. Use a larger size then downcast where needed

### Findings

Total:5

[maverick-v1/contracts/libraries/BinMap.sol#L76](https://github.com/code-423n4/2022-12-Stealth-Project/blob/main//maverick-v1/contracts/libraries/BinMap.sol#L76)

```solidity
76:    int32 refTick = isRight ? tick + 1 : tick;
```

[maverick-v1/contracts/libraries/BinMap.sol#L101](https://github.com/code-423n4/2022-12-Stealth-Project/blob/main//maverick-v1/contracts/libraries/BinMap.sol#L101)

```solidity
101:    int32 posFirst = mapIndex * int16(WORD_SIZE) + int16(subIndex);
```

[maverick-v1/contracts/libraries/BinMap.sol#L56](https://github.com/code-423n4/2022-12-Stealth-Project/blob/main//maverick-v1/contracts/libraries/BinMap.sol#L56)

```solidity
56:    int32 pos = (wordIndex * int16(WORD_SIZE) + int16(offset));
```

[maverick-v1/contracts/models/Pool.sol#L487](https://github.com/code-423n4/2022-12-Stealth-Project/blob/main//maverick-v1/contracts/models/Pool.sol#L487)

```solidity
487:    int32 thisBinTick = isDelta ? currentState.activeTick + pos : pos;
```

[maverick-v1/contracts/models/Pool.sol#L619](https://github.com/code-423n4/2022-12-Stealth-Project/blob/main//maverick-v1/contracts/models/Pool.sol#L619)

```solidity
619:    int32 activeTick = delta.decrementTick ? currentState.activeTick - 1 : currentState.activeTick;
```

## [G-007] Use named returns for local variables where it is possible.

### Impact

It is not necessary to have both a named return and a return statement.

### Findings

Total:1

[maverick-v1/contracts/libraries/BinMath.sol#L91-L116](https://github.com/code-423n4/2022-12-Stealth-Project/blob/main//maverick-v1/contracts/libraries/BinMath.sol#L91-L116)

```solidity
    function tickSqrtPrice(uint256 tickSpacing, int32 _tick) internal pure returns (uint256 _result) {
        uint256 tick = _tick < 0 ? uint256(-int256(_tick)) : uint256(int256(_tick));
        tick *= tickSpacing;
        require(tick <= MAX_TICK, "X");
		...
		...
        uint256 result = (ratio * PRBMathUD60x18.SCALE) >> 128;

		return result;
    }
```