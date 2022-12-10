## ACL checks are missing

Contract:
https://github.com/code-423n4/2022-12-Stealth-Project/blob/main/maverick-v1/contracts/models/Position.sol#L28

Impact:
Since ACL checks are missing anybody can mint tokens by calling the `mint` function

Steps:

1. Observe the `mint` function

```
function mint(address to) external returns (uint256 tokenId) {
        tokenId = _tokenIdCounter.current();
        _tokenIdCounter.increment();
        _safeMint(to, tokenId);
    }
```

2. Observe that there is no ACL and anyone can call this function to mint any number of token they wish

Recommendation:
The `mint` function should only be callable via `Router` contract 