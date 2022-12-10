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
