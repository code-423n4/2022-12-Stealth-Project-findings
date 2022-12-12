### [L-01] `Pool.checkPositionAccess()` doesn't check the `approvedForAll` users.
- https://github.com/code-423n4/2022-12-Stealth-Project/blob/fc8589d7d8c1d8488fd97ccc46e1ff11c8426ac2/maverick-v1/contracts/models/Pool.sol#L86-L88

```solidity
    function checkPositionAccess(uint256 tokenId) internal view {
        require(msg.sender == position.ownerOf(tokenId) || msg.sender == position.getApproved(tokenId), "P");
    }
```

Currently, it checks only the owner or approved user for the `tokenId` for the access permission.

But according to the [openzeppelin implement](https://github.com/OpenZeppelin/openzeppelin-contracts/blob/master/contracts/token/ERC721/ERC721.sol#L239), it should allow `isApprovedForAll` users as well because such users can be the token owner without any requirements by transferring the token.

```solidity
    function _isApprovedOrOwner(address spender, uint256 tokenId) internal view virtual returns (bool) {
        address owner = ERC721.ownerOf(tokenId);
        return (spender == owner || isApprovedForAll(owner, spender) || getApproved(tokenId) == spender);
    }
```

### [L-02] `Math.mulDiv()` returns a wrong value when `ceil = true`
- https://github.com/code-423n4/2022-12-Stealth-Project/blob/fc8589d7d8c1d8488fd97ccc46e1ff11c8426ac2/maverick-v1/contracts/libraries/Math.sol#L35

```solidity
    function mulDiv(uint256 x, uint256 y, uint256 k, bool ceil) internal pure returns (uint256) {
        if (y == k) return x;
        return ceil ? PRBMath.mulDiv(x, y, k) + 1 : PRBMath.mulDiv(x, y, k);
    }
```

Currently, `ceilVal = floorVal + 1` all the time but they should be same when `PRBMath.mulDiv(x, y, k)` is an exact uint value without roundings.

### [L-03] `Pool.setProtocolFeeRatio()` should have a upper limit.
- https://github.com/code-423n4/2022-12-Stealth-Project/blob/fc8589d7d8c1d8488fd97ccc46e1ff11c8426ac2/maverick-v1/contracts/models/Pool.sol#L332
- https://github.com/code-423n4/2022-12-Stealth-Project/blob/fc8589d7d8c1d8488fd97ccc46e1ff11c8426ac2/maverick-v1/contracts/models/Factory.sol#L26-L38

When we set a `protocolFeeRatio`, it can be `ONE_3_DECIMAL_SCALE` which means 100% of the pool fee.

Then all amounts of `feeBasis` will be a protocol fee [here](https://github.com/code-423n4/2022-12-Stealth-Project/blob/fc8589d7d8c1d8488fd97ccc46e1ff11c8426ac2/maverick-v1/contracts/models/Pool.sol#L549-L551), no benefit for liquidity providers.

### [L-04] Check address(0) for recipient
- https://github.com/code-423n4/2022-12-Stealth-Project/blob/fc8589d7d8c1d8488fd97ccc46e1ff11c8426ac2/maverick-v1/contracts/models/Pool.sol#L260
- https://github.com/code-423n4/2022-12-Stealth-Project/blob/fc8589d7d8c1d8488fd97ccc46e1ff11c8426ac2/maverick-v1/contracts/models/Pool.sol#L209
- https://github.com/code-423n4/2022-12-Stealth-Project/blob/fc8589d7d8c1d8488fd97ccc46e1ff11c8426ac2/maverick-v1/contracts/models/Pool.sol#L339

When users or admin set the `recipient`, there is no validation of address(0) so the funds might be lost.

### [N-01] Typo
- https://github.com/code-423n4/2022-12-Stealth-Project/blob/fc8589d7d8c1d8488fd97ccc46e1ff11c8426ac2/maverick-v1/contracts/interfaces/IPool.sol#L60

"amount of B token in bin" => "amount of A token in bin"


