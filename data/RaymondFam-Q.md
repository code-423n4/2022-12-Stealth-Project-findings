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
## Lines too long
Lines in source code are typically limited to 80 characters, but it’s reasonable to stretch beyond this limit when need be as monitor screens theses days are comparatively larger. Considering the files will most likely reside in GitHub that will have a scroll bar automatically kick in when the length is over 164 characters, all code lines and comments should be split when/before hitting this length. Keep line width to max 120 characters for better readability where possible.

Here are some of the instances entailed:

[File: Factory.sol#L58](https://github.com/code-423n4/2022-12-Stealth-Project/blob/main/maverick-v1/contracts/models/Factory.sol#L58)
[File: Pool.sol#L238](https://github.com/code-423n4/2022-12-Stealth-Project/blob/main/maverick-v1/contracts/models/Pool.sol#L238)
[File: Pool.sol#L260](https://github.com/code-423n4/2022-12-Stealth-Project/blob/main/maverick-v1/contracts/models/Pool.sol#L260)
[File: Pool.sol#L270](https://github.com/code-423n4/2022-12-Stealth-Project/blob/main/maverick-v1/contracts/models/Pool.sol#L270)
[File: Pool.sol#L294](https://github.com/code-423n4/2022-12-Stealth-Project/blob/main/maverick-v1/contracts/models/Pool.sol#L294)
[File: Pool.sol#L516](https://github.com/code-423n4/2022-12-Stealth-Project/blob/main/maverick-v1/contracts/models/Pool.sol#L516)
[File: Pool.sol#L534]( https://github.com/code-423n4/2022-12-Stealth-Project/blob/main/maverick-v1/contracts/models/Pool.sol#L534)
[File: Pool.sol#L597](https://github.com/code-423n4/2022-12-Stealth-Project/blob/main/maverick-v1/contracts/models/Pool.sol#L597)
[File: Pool.sol#L602](https://github.com/code-423n4/2022-12-Stealth-Project/blob/main/maverick-v1/contracts/models/Pool.sol#L602)
[File: Pool.sol#L639](https://github.com/code-423n4/2022-12-Stealth-Project/blob/main/maverick-v1/contracts/models/Pool.sol#L639)
[File: PoolInspector.sol#L33](https://github.com/code-423n4/2022-12-Stealth-Project/blob/main/maverick-v1/contracts/models/PoolInspector.sol#L33)
[File: PoolInspector.sol#L107](https://github.com/code-423n4/2022-12-Stealth-Project/blob/main/maverick-v1/contracts/models/PoolInspector.sol#L107)

## Inadequate NatSpec
Solidity contracts can use a special form of comments, i.e., the Ethereum Natural Language Specification Format (NatSpec) to provide rich documentation for functions, return variables and more. Please visit the following link for further details:

https://docs.soliditylang.org/en/v0.8.16/natspec-format.html

Here are some of the contract instances partially lacking NatSpec:

[File: Factory.sol](https://github.com/code-423n4/2022-12-Stealth-Project/blob/main/maverick-v1/contracts/models/Factory.sol)
[File: Router.sol](https://github.com/code-423n4/2022-12-Stealth-Project/blob/main/router-v1/contracts/Router.sol)

Here are some of the contract instances lacking NatSpec in its entirety:

[File: Deployer.sol](https://github.com/code-423n4/2022-12-Stealth-Project/blob/main/maverick-v1/contracts/libraries/Deployer.sol)
[File: Pool.sol](https://github.com/code-423n4/2022-12-Stealth-Project/blob/main/maverick-v1/contracts/models/Pool.sol)
[File: PoolInspector.sol](https://github.com/code-423n4/2022-12-Stealth-Project/blob/main/maverick-v1/contracts/models/PoolInspector.sol)
[File: Position.sol](https://github.com/code-423n4/2022-12-Stealth-Project/blob/main/maverick-v1/contracts/models/Position.sol)

## Modularity on import usages
For cleaner Solidity code in conjunction with the rule of modularity and modular programming, import only what you need with curly braces instead of adopting the global import approach. 

For instance, the import instances below could be refactored as follows:

[File: PoolInspector.sol#L4-L9](https://github.com/code-423n4/2022-12-Stealth-Project/blob/main/maverick-v1/contracts/models/PoolInspector.sol#L4-L9)

```
import {Pool} from "./Pool.sol";
import {ISwapCallback} from t "../interfaces/ISwapCallback.sol";
import {BinMath, PRBMathUD60x18} from"../libraries/Bin.sol";
import {Math} from "../libraries/Math.sol";
import {Constants} from "../libraries/Constants.sol";
import {IPool} from "../interfaces/IPool.sol";
```
## Two-step transfer of ownership
When setting a new owner, it is entirely possible to accidentally transfer ownership to an uncontrolled and/or non-valid account, breaking all function calls requiring `msg.sender == owner`. Consider implementing a two step process where the current owner nominates an account that would need to call `acceptOwnership()` for the transfer of ownership to fully succeed. This will also ensure the new owner is going to be fully aware of the ownership assigned/transferred. 

Here is the instance entailed:

[File: Factory.sol#L40-L44](https://github.com/code-423n4/2022-12-Stealth-Project/blob/main/maverick-v1/contracts/models/Factory.sol#L40-L44)

```
    function setOwner(address _owner) external {
        require(msg.sender == owner && _owner != address(0), "Factory:NOT_ALLOWED");
        owner = _owner;
        emit SetFactoryOwner(_owner);
    }
```
## Events associated with setter functions
Consider having events associated with setter functions emit both the new and old values instead of just the new value.

Here are some of the instances entailed:

[File: Factory.sol](https://github.com/code-423n4/2022-12-Stealth-Project/blob/main/maverick-v1/contracts/models/Factory.sol)

```
37:        emit SetFactoryProtocolFeeRatio(_protocolFeeRatio);

43:        emit SetFactoryOwner(_owner);
```
[File: Position.sol#L25](https://github.com/code-423n4/2022-12-Stealth-Project/blob/main/maverick-v1/contracts/models/Position.sol#L25)

```
        emit SetMetadata(metadata);
```
## Sanity checks at the constructor
Adequate zero address and zero value checks should be implemented at the constructor to avoid accidental error(s) that could result in non-functional calls associated with it particularly when assigning immutable variables.

Here are some of the instances entailed:

[File: Pool.sol#L73-L82](https://github.com/code-423n4/2022-12-Stealth-Project/blob/main/maverick-v1/contracts/models/Pool.sol#L73-L82)

```
        fee = _fee;
        state.protocolFeeRatio = _protocolFeeRatio;
        tickSpacing = _tickSpacing;
        state.activeTick = _activeTick;
        twa.lookback = _lookback;
        tokenA = _tokenA;
        tokenB = _tokenB;
        position = _position;
        tokenAScale = _tokenAScale;
        tokenBScale = _tokenBScale;
```
## Descriptive and Verbose Options
Consider making the error messages more verbose and distinct so that all other peer developers would better be able to comprehend the intended statement logic, significantly enhancing the code readability.

Here are some of the instances entailed:

[File: Pool.sol](https://github.com/code-423n4/2022-12-Stealth-Project/blob/main/maverick-v1/contracts/models/Pool.sol)

```
87:        require(msg.sender == position.ownerOf(tokenId) || msg.sender == position.getApproved(tokenId), "P");

93:        require((currentState.status & LOCKED == 0) && (allowInEmergency || (currentState.status & EMERGENCY == 0)), "E");

168:        require(previousABalance + tokenAAmount <= _tokenABalance() && previousBBalance + tokenBAmount <= _tokenBBalance(), "A");

303:        require(previousBalance + amountIn <= (tokenAIn ? _tokenABalance() : _tokenBBalance()), "S");
```
## Unneeded `return` keyword on named returns
Functions adopting named returns need not use the `return` keyword when returning their corresponding values.

For instance, the two return instances below may be refactored as follows:

[File: Pool.sol#L312-L318](https://github.com/code-423n4/2022-12-Stealth-Project/blob/main/maverick-v1/contracts/models/Pool.sol#L312-L318)

```
    function getBin(uint128 binId) external view override returns (BinState memory bin) {
-        return bins[binId].state;
+        bins[binId].state;
    }

    function balanceOf(uint256 tokenId, uint128 binId) external view override returns (uint256 lpToken) {
-        return bins[binId].balances[tokenId];
+        bins[binId].balances[tokenId];
    }
```
## Use `delete` to clear variables
`delete a` assigns the initial value for the type to `a`. i.e. for integers it is equivalent to `a = 0`, but it can also be used on arrays, where it assigns a dynamic array of length zero or a static array of the same length with all elements reset. For structs, it assigns a struct with all members reset. Similarly, it can also be used to set an address to zero address or a boolean to false. It has no effect on whole mappings though (as the keys of mappings may be arbitrary and are generally unknown). However, individual keys and what they map to can be deleted: If `a` is a mapping, then `delete a[x]` will delete the value stored at x.

The delete key better conveys the intention and is also more idiomatic.

For instance, the `a = 0` instance below may be refactored as follows when `_removeBin()` is internally invoked:

[File: Pool.sol#L359](https://github.com/code-423n4/2022-12-Stealth-Project/blob/main/maverick-v1/contracts/models/Pool.sol#L359)

```
-        binPositions[tick][kind] = 0;
+        delete binPositions[tick][kind];
```
Similarly, the `a = false` instance below may be refactored as follows after `_removeBin()` is internally invoked:

[File: Pool.sol#L235](https://github.com/code-423n4/2022-12-Stealth-Project/blob/main/maverick-v1/contracts/models/Pool.sol#L235)

```
-                isActive = false;
+                delete isActive;
```
## Emission of events at the constructor
State variable assignment that entails an event emission should also be inclusive at the construct to be consistent with the intended design. 

For instance, the constructor instance below may be refactored as follows:

[File: Position.sol#L19](https://github.com/code-423n4/2022-12-Stealth-Project/blob/main/maverick-v1/contracts/models/Position.sol#L19)

```
-        metadata = _metadata;
+        setMetadata( _metadata);
```
## Missing checks for contract existence
Performing low-level calls without confirming contract’s existence (not yet deployed or have been destructed) could return success even though no function call was executed as documented in the link below:

https://docs.soliditylang.org/en/v0.8.7/control-structures.html#error-handling-assert-require-revert-and-exceptions

Consider having account existence checked prior to calling if needed.

Note: A report associated with `Pool.sol` and `SafeERC20Min.sol` delineating the malicious attack has been separately submitted.  

Here are the other instances entailed that may not have posed a risk but for informational purpose:

[File: TransferHelper.sol](https://github.com/code-423n4/2022-12-Stealth-Project/blob/main/router-v1/contracts/libraries/TransferHelper.sol)

```
14:        (bool success, bytes memory data) = token.call(abi.encodeWithSelector(IERC20.transferFrom.selector, from, to, value));

24:        (bool success, bytes memory data) = token.call(abi.encodeWithSelector(IERC20.transfer.selector, to, value));

34:        (bool success, bytes memory data) = token.call(abi.encodeWithSelector(IERC20.approve.selector, to, value));

43:        (bool success, ) = to.call{value: value}(new bytes(0));
```
## Use enum for lock and action states
Consider adopting enum(s) on the following sets of constants to make the code more presentable and structured:

[File: Pool.sol#L31-L38](https://github.com/code-423n4/2022-12-Stealth-Project/blob/main/maverick-v1/contracts/models/Pool.sol#L31-L38)

```
    uint8 constant NO_EMERGENCY_UNLOCKED = 0;
    uint8 constant LOCKED = 1;
    uint8 constant EMERGENCY = 2;

    uint256 constant ACTION_EMERGENCY_MODE = 911;
    uint256 constant ACTION_SET_PROTOCOL_FEES = 1;
    uint256 constant ACTION_CLAIM_PROTOCOL_FEES_A = 2;
    uint256 constant ACTION_CLAIM_PROTOCOL_FEES_B = 3;
```
## Rebasing tokens
The protocol does not mention whether or not rebasing tokens will be supported. But, if they will be, traders can steal output token for which `swapCallback()` can be invoked by expanding the total supply of the rebasing token.

[File: Pool.sol#L302](https://github.com/code-423n4/2022-12-Stealth-Project/blob/main/maverick-v1/contracts/models/Pool.sol#L302)

```
        ISwapCallback(msg.sender).swapCallback(amountIn, amountOut, data);
```
Apparently, the contract associated with the external call above does not have to be a shared router and can be separately implemented by the trader. For a created pool, it is possible that the input token is rebasing, allowing anyone to trigger the rebasing event. When calling `swap()`, `swapCallback()` in the callee contract implemented by the trader can be executed to call another contract, which has the admin privilege for the rebasing token to trigger the rebasing event. When the rebasing event expands the input token's total supply, calling `swapCallback()` does not need to send additionally enough input token to the pool, and the pool's balance of the input rebasing token can still become larger enough than its previous input token balance. For this reason, calling `swap()` will not revert in this situation, and the trader can receive the correspondingly extra output token amount although the trader has not sent in enough input tokens to the pool.