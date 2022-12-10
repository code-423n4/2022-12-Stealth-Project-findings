## Non-Critical

### Lack revert reason

e.g.

https://github.com/code-423n4/2022-12-Stealth-Project/blob/fc8589d7d8c1d8488fd97ccc46e1ff11c8426ac2/maverick-v1/contracts/models/Pool.sol#L129

```solidity
            require(param.kind < NUMBER_OF_KINDS);
```

### Missing license

The project is supposed to be under BSL but most file are UNLICENSED

https://github.com/code-423n4/2022-12-Stealth-Project/blob/fc8589d7d8c1d8488fd97ccc46e1ff11c8426ac2/maverick-v1/contracts/models/Pool.sol#L1-L2

```solidity
// SPDX-License-Identifier: UNLICENSED

```