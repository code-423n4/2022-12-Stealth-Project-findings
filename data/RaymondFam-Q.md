## Sort `_tokenA` and `_takenB` on `create()`
In `create()` of `Factory.sol`, instead of reverting the function on the first require check if the tokens have not been sorted, consider having the function logic refactored as follows to better facilitate the function call:

[File: Factory.sol#L58-L83](https://github.com/code-423n4/2022-12-Stealth-Project/blob/main/maverick-v1/contracts/models/Factory.sol#L58-L83)

```
59: - require(_tokenA < _tokenB, "Factory:TOKENS_MUST_BE_SORTED_BY_ADDRESS");
    + (IERC20 tokenA, IERC20 tokenB) = _tokenA < _tokenB ? (_tokenA, _tokenB) : (_tokenB, _tokenA);
...
72: - _tokenA
73: - _tokenB
    + tokenA
    + tokenB
...
80: - emit PoolCreated(address(pool), _fee, _tickSpacing, _activeTick, _lookback, protocolFeeRatio, _tokenA, _tokenB);
    + emit PoolCreated(address(pool), _fee, _tickSpacing, _activeTick, _lookback, protocolFeeRatio, tokenA, tokenB);
```  
## Lines Too Long
Lines in source code are typically limited to 80 characters, but it’s reasonable to stretch beyond this limit when need be as monitor screens theses days are comparatively larger. Considering the files will most likely reside in GitHub that will have a scroll bar automatically kick in when the length is over 164 characters, all code lines and comments should be split when/before hitting this length. Keep line width to max 120 characters for better readability where possible.

Here are the instances entailed:

[File: Factory.sol#L58](https://github.com/code-423n4/2022-12-Stealth-Project/blob/main/maverick-v1/contracts/models/Factory.sol#L58)

## Inadequate NatSpec
Solidity contracts can use a special form of comments, i.e., the Ethereum Natural Language Specification Format (NatSpec) to provide rich documentation for functions, return variables and more. Please visit the following link for further details:

https://docs.soliditylang.org/en/v0.8.16/natspec-format.html

Here are the contract instances partially lacking NatSpec:

[File: Factory.sol](https://github.com/code-423n4/2022-12-Stealth-Project/blob/main/maverick-v1/contracts/models/Factory.sol)
