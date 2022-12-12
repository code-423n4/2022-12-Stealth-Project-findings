# GAS
## Empty blocks should be removed or emit something
### Impact
The code should be refactored such that they no longer exist, or the block should do something useful, such as emitting an event or reverting. If the contract is meant to be extended, the contract should be abstract and the function signatures be added without any default implementation.

### Github Permalinks
https://github.com/code-423n4/2022-12-Stealth-Project/blob/2c798a70333c83102affd19c4346412be4bf859e/maverick-v1/contracts/models/PoolInspector.sol#L65
        try pool.swap(address(this), amount, tokenAIn, exactOutput, 0, abi.encode(exactOutput)) {} catch Error(string memory _data) {


### Mitigation
Remove empty block, revert or emit something.


## Increments can be unchecked in loops
### Summary
Unchecked operations as the ++i on for loops are cheaper than checked one.

### Details
In Solidity 0.8+, there’s a default overflow check on unsigned integers. It’s possible to uncheck this in for-loops and save some gas at each iteration, but at the cost of some code readability, as this uncheck cannot be made inline..

    The code would go from:
```
    for (uint256 i; i < numIterations; i++) {
    // ...
    }
```

    to
```
    for (uint256 i; i < numIterations;) {
      // ...
      unchecked { ++i; }
    }
    The risk of overflow is inexistent for a uint256 here.
```

### Github Permalinks


https://github.com/code-423n4/2022-12-Stealth-Project/blob/2c798a70333c83102affd19c4346412be4bf859e/maverick-v1/contracts/models/Pool.sol#L127
        for (uint256 i; i < params.length; i++) {

https://github.com/code-423n4/2022-12-Stealth-Project/blob/2c798a70333c83102affd19c4346412be4bf859e/maverick-v1/contracts/models/Pool.sol#L180
        for (uint256 i; i < binIds.length; i++) {

https://github.com/code-423n4/2022-12-Stealth-Project/blob/2c798a70333c83102affd19c4346412be4bf859e/maverick-v1/contracts/models/Pool.sol#L193
        for (uint256 i; i < params.length; i++) {

https://github.com/code-423n4/2022-12-Stealth-Project/blob/2c798a70333c83102affd19c4346412be4bf859e/maverick-v1/contracts/models/Pool.sol#L224
        for (uint256 i; i < params.length; i++) {

https://github.com/code-423n4/2022-12-Stealth-Project/blob/2c798a70333c83102affd19c4346412be4bf859e/maverick-v1/contracts/models/Pool.sol#L380
        for (uint256 j; j < 2; j++) {

https://github.com/code-423n4/2022-12-Stealth-Project/blob/2c798a70333c83102affd19c4346412be4bf859e/maverick-v1/contracts/models/Pool.sol#L389
            for (uint256 i; i <= moveData.binCounter; i++) {

https://github.com/code-423n4/2022-12-Stealth-Project/blob/2c798a70333c83102affd19c4346412be4bf859e/maverick-v1/contracts/models/Pool.sol#L414
            for (uint256 i; i < moveData.mergeBinCounter; i++) {

https://github.com/code-423n4/2022-12-Stealth-Project/blob/2c798a70333c83102affd19c4346412be4bf859e/maverick-v1/contracts/models/Pool.sol#L523
        for (uint256 i; i < NUMBER_OF_KINDS; i++) {

https://github.com/code-423n4/2022-12-Stealth-Project/blob/2c798a70333c83102affd19c4346412be4bf859e/maverick-v1/contracts/models/Pool.sol#L540
        for (uint256 i; i < NUMBER_OF_KINDS; i++) {

https://github.com/code-423n4/2022-12-Stealth-Project/blob/2c798a70333c83102affd19c4346412be4bf859e/maverick-v1/contracts/models/Pool.sol#L653
            for (uint256 i; i < swapData.counter; i++) {

https://github.com/code-423n4/2022-12-Stealth-Project/blob/2c798a70333c83102affd19c4346412be4bf859e/maverick-v1/contracts/models/PoolInspector.sol#L30
        for (uint128 i = startBinIndex; i < binCounter; i++) {

https://github.com/code-423n4/2022-12-Stealth-Project/blob/2c798a70333c83102affd19c4346412be4bf859e/maverick-v1/contracts/models/PoolInspector.sol#L80
        for (uint8 i = 0; i < NUMBER_OF_KINDS; i++) {

https://github.com/code-423n4/2022-12-Stealth-Project/blob/2c798a70333c83102affd19c4346412be4bf859e/maverick-v1/contracts/models/PoolInspector.sol#L101
        for (uint256 i; i < bins.length; i++) {

https://github.com/code-423n4/2022-12-Stealth-Project/blob/2c798a70333c83102affd19c4346412be4bf859e/router-v1/contracts/libraries/Multicall.sol#L13
        for (uint256 i = 0; i < data.length; i++) {



### Mitigation
Add unchecked `++i` at the end of all the for loop where it's not expected to overflow and remove them from the for header



## Pack structs tightly
### Summary
Gas efficiency can be achieved by tightly packing the struct. Struct variables are stored in 32 bytes each so you can group smaller types to occupy less storage. 

### Details
You can read more here: https://fravoll.github.io/solidity-patterns/tight_variable_packing.html or in the official documentation: https://docs.soliditylang.org/en/v0.4.21/miscellaneous.html
### Github Permalinks
https://github.com/code-423n4/2022-12-Stealth-Project/blob/2c798a70333c83102affd19c4346412be4bf859e/maverick-v1/contracts/models/PoolInspector.sol#L14
    struct BinInfo {

https://github.com/code-423n4/2022-12-Stealth-Project/blob/2c798a70333c83102affd19c4346412be4bf859e/maverick-v1/contracts/libraries/Delta.sol#L5
    struct Instance {

https://github.com/code-423n4/2022-12-Stealth-Project/blob/2c798a70333c83102affd19c4346412be4bf859e/maverick-v1/contracts/interfaces/IPool.sol#L34
    struct BinDelta {

https://github.com/code-423n4/2022-12-Stealth-Project/blob/2c798a70333c83102affd19c4346412be4bf859e/maverick-v1/contracts/models/Pool.sol#L363
    struct MoveData {     


### Mitigation
Order structs to reduce gas usage.


## `abi.encode()` is less gas efficient than `abi.encodePacked()`
### Summary
In general, `abi.encodePacked` is more gas-efficient.

### Details
Changing the abi.encode function to `abi.encodePacked` can save gas since the abi.encode function pads extra null bytes at the end of the call data, which is unnecessary. Also, in general, `abi.encodePacked` is more gas-efficient.
### Github Permalinks


https://github.com/code-423n4/2022-12-Stealth-Project/blob/2c798a70333c83102affd19c4346412be4bf859e/maverick-v1/contracts/models/PoolInspector.sol#L58
            revert(string(abi.encode(amountIn)));

https://github.com/code-423n4/2022-12-Stealth-Project/blob/2c798a70333c83102affd19c4346412be4bf859e/maverick-v1/contracts/models/PoolInspector.sol#L60
            revert(string(abi.encode(amountOut)));

https://github.com/code-423n4/2022-12-Stealth-Project/blob/2c798a70333c83102affd19c4346412be4bf859e/maverick-v1/contracts/models/PoolInspector.sol#L65
        try pool.swap(address(this), amount, tokenAIn, exactOutput, 0, abi.encode(exactOutput)) {} catch Error(string memory _data) {

https://github.com/code-423n4/2022-12-Stealth-Project/blob/2c798a70333c83102affd19c4346412be4bf859e/router-v1/contracts/Router.sol#L128
        (, amountOut) = pool.swap(recipient, amountIn, tokenAIn, false, sqrtPriceLimitD18, abi.encode(data));

https://github.com/code-423n4/2022-12-Stealth-Project/blob/2c798a70333c83102affd19c4346412be4bf859e/router-v1/contracts/Router.sol#L176
        (amountIn, amountOutReceived) = pool.swap(recipient, amountOut, tokenAIn, true, 0, abi.encode(data));

https://github.com/code-423n4/2022-12-Stealth-Project/blob/2c798a70333c83102affd19c4346412be4bf859e/router-v1/contracts/Router.sol#L232
        (tokenAAmount, tokenBAmount, binDeltas) = pool.addLiquidity(tokenId, params, abi.encode(data));


### Mitigation
Consider changing abi.encode to `abi.encodePacked`


