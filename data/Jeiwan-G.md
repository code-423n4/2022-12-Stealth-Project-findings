# [G-01] An approved operator is not allow to manage liquidity
## Targets
- https://github.com/code-423n4/2022-12-Stealth-Project/blob/fc8589d7d8c1d8488fd97ccc46e1ff11c8426ac2/maverick-v1/contracts/models/Pool.sol#L87 
## Impact
An approved operator won't be allowed to manage liquidity on behalf of a token owner. This makes management of multiple liquidity positions less flexible and increases costs for liquidity providers since they'll need to call `approve` for each of the tokens they own.
## Proof of Concept
The `checkPositionAccess` function of Pool contract controls who can transfer or remove liquidity belonging to a token owner ([Pool.sol#L192](https://github.com/code-423n4/2022-12-Stealth-Project/blob/fc8589d7d8c1d8488fd97ccc46e1ff11c8426ac2/maverick-v1/contracts/models/Pool.sol#L192), [Pool.sol#L217](https://github.com/code-423n4/2022-12-Stealth-Project/blob/fc8589d7d8c1d8488fd97ccc46e1ff11c8426ac2/maverick-v1/contracts/models/Pool.sol#L217)). The function allows only the token owner or an approved address:
```solidity
function checkPositionAccess(uint256 tokenId) internal view {
    require(msg.sender == position.ownerOf(tokenId) || msg.sender == position.getApproved(tokenId), "P");
}
```

However, it doesn't check for an operator approval. The approvals mechanism in ERC721 is a little bit more complicated than the one in ERC20: due to the non-fungible nature of ERC721 tokens, it's impractical to approve many tokens by calling `approve` on each of them. Instead, the operator approval mechanism was added: [ERC721.sol#L136-L145](https://github.com/OpenZeppelin/openzeppelin-contracts/blob/3d7a93876a2e5e1d7fe29b5a0e96e222afdc4cfa/contracts/token/ERC721/ERC721.sol#L136-L145)–it allows a token owner to approve an operator to manage all their tokens. This makes tokens management delegation an easier and much more cheaper task.
## Tools Used
Manual review
## Recommended Mitigation Steps
Consider this change:
```solidity
--- a/maverick-v1/contracts/models/Pool.sol
+++ b/maverick-v1/contracts/models/Pool.sol
@@ -84,13 +84,16 @@ contract Pool is IPool, IPoolAdmin {
     }

     function checkPositionAccess(uint256 tokenId) internal view {
-        require(msg.sender == position.ownerOf(tokenId) || msg.sender == position.getApproved(tokenId), "P");
+        require(
+            msg.sender == position.ownerOf(tokenId) ||
+            msg.sender == position.getApproved(tokenId) ||
+            position.isApprovedForAll(position.ownerOf(tokenId), msg.sender), "P");
     }
```
And consider exposing the `_isApprovedOrOwner` function ([ERC721.sol#L239-L242](https://github.com/OpenZeppelin/openzeppelin-contracts/blob/3d7a93876a2e5e1d7fe29b5a0e96e222afdc4cfa/contracts/token/ERC721/ERC721.sol#L239-L242)) and calling it instead, to reduce the cost of the external calls.