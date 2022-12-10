## Type casting constant is evaluate in runtime

https://github.com/code-423n4/2022-12-Stealth-Project/blob/fc8589d7d8c1d8488fd97ccc46e1ff11c8426ac2/maverick-v1/contracts/libraries/BinMap.sol#L11

```solidity
    int32 constant NUMBER_OF_KINDS_32 = int32(uint32(NUMBER_OF_KINDS));
```

Alternatively, set it along side NUMBER_OF_KINDS, e.g.

```solidity
uint8 constant NUMBER_OF_KINDS = 4;
int32 constant NUMBER_OF_KINDS_32 = 4;
```

and add relavent test cases to make sure they equal

## Status can be set to NO_EMERGENCY_UNLOCKED if it does not allow emergency to begin with

For example, transferLiquidity does not allow emergency

https://github.com/code-423n4/2022-12-Stealth-Project/blob/fc8589d7d8c1d8488fd97ccc46e1ff11c8426ac2/maverick-v1/contracts/models/Pool.sol#L188

```solidity
        checkReentrancy(false, true);
```

when exiting, we instead of removing the lock bit, we can simply set to NO_EMERGENCY_UNLOCKED

https://github.com/code-423n4/2022-12-Stealth-Project/blob/fc8589d7d8c1d8488fd97ccc46e1ff11c8426ac2/maverick-v1/contracts/models/Pool.sol#L205

```solidity
        state.status &= ~LOCKED;
```

## Use hash instead of nested mapping

Can instead use a bytes32 => IPool mapping with keecak256(abi.encode(_fee, _tickSpacing, _lookback, _tokenA, _tokenB))

https://github.com/code-423n4/2022-12-Stealth-Project/blob/fc8589d7d8c1d8488fd97ccc46e1ff11c8426ac2/maverick-v1/contracts/models/Factory.sol#L64-L65

```solidity
        require(pools[_fee][_tickSpacing][_lookback][_tokenA][_tokenB] == IPool(address(0)), "Factory:POOL_ALREADY_EXISTS");

```
