## Summary<a name="Summary">

### Low Risk Issues
| |Issue|Contexts|
|-|:-|:-:|
| [LOW&#x2011;1](#LOW&#x2011;1) | `decimals()` not part of ERC20 standard | 2 |
| [LOW&#x2011;2](#LOW&#x2011;2) | Missing Contract-existence Checks Before Low-level Calls | 5 |
| [LOW&#x2011;3](#LOW&#x2011;3) | Contracts are not using their OZ Upgradeable counterparts | 22 |
| [LOW&#x2011;4](#LOW&#x2011;4) | Low Level Calls With Solidity Version 0.8.14 Can Result In Optimiser Bug | 3 |
| [LOW&#x2011;5](#LOW&#x2011;5) | Upgrade OpenZeppelin Contract Dependency | 1 |

Total: 33 contexts over 5 issues

### Non-critical Issues
| |Issue|Contexts|
|-|:-|:-:|
| [NC&#x2011;1](#NC&#x2011;1) | Critical Changes Should Use Two-step Procedure | 5 |
| [NC&#x2011;2](#NC&#x2011;2) | Use a more recent version of Solidity | 34 |
| [NC&#x2011;3](#NC&#x2011;3) | Event Is Missing Indexed Fields | 3 |
| [NC&#x2011;4](#NC&#x2011;4) | Missing event for critical parameter change | 1 |
| [NC&#x2011;5](#NC&#x2011;5) | Implementation contract may not be initialized | 5 |
| [NC&#x2011;6](#NC&#x2011;6) | Use of Block.Timestamp | 2 |
| [NC&#x2011;7](#NC&#x2011;7) | Non-usage of specific imports | 60 |
| [NC&#x2011;8](#NC&#x2011;8) | Use `bytes.concat()` | 3 |
| [NC&#x2011;9](#NC&#x2011;9) | No need to initialize uints to zero | 1 |
| [NC&#x2011;10](#NC&#x2011;10) | Use `delete` to Clear Variables | 1 |

Total: 115 contexts over 10 issues

## Low Risk Issues



### <a href="#Summary">[LOW&#x2011;1]</a><a name="LOW&#x2011;1"> `decimals()` not part of ERC20 standard

`decimals()` is not part of the official ERC20 standard and might fail for tokens that do not implement it. While in practice it is very unlikely, as usually most of the tokens implement it, this should still be considered as a potential issue.

#### <ins>Proof Of Concept</ins>


```solidity
74: Math.scale(IERC20Metadata(address(_tokenA)).decimals()
```

https://github.com/code-423n4/2022-12-Stealth-Project/tree/main/maverick-v1/contracts/models/Factory.sol#L74

```solidity
75: Math.scale(IERC20Metadata(address(_tokenB)).decimals()
```

https://github.com/code-423n4/2022-12-Stealth-Project/tree/main/maverick-v1/contracts/models/Factory.sol#L75






### <a href="#Summary">[LOW&#x2011;2]</a><a name="LOW&#x2011;2"> Missing Contract-existence Checks Before Low-level Calls

Low-level calls return success if there is no code present at the specified address. 

#### <ins>Proof Of Concept</ins>


```solidity
14: (bool success, bytes memory result) = address(this).delegatecall(data[i]);
```

https://github.com/code-423n4/2022-12-Stealth-Project/tree/main/router-v1/contracts/libraries/Multicall.sol#L14

```solidity
14: (bool success, bytes memory data) = token.call(abi.encodeWithSelector(IERC20.transferFrom.selector, from, to, value));
```

https://github.com/code-423n4/2022-12-Stealth-Project/tree/main/router-v1/contracts/libraries/TransferHelper.sol#L14

```solidity
24: (bool success, bytes memory data) = token.call(abi.encodeWithSelector(IERC20.transfer.selector, to, value));
```

https://github.com/code-423n4/2022-12-Stealth-Project/tree/main/router-v1/contracts/libraries/TransferHelper.sol#L24

```solidity
34: (bool success, bytes memory data) = token.call(abi.encodeWithSelector(IERC20.approve.selector, to, value));
```

https://github.com/code-423n4/2022-12-Stealth-Project/tree/main/router-v1/contracts/libraries/TransferHelper.sol#L34

```solidity
43: (bool success, ) = to.call{value: value}(new bytes(0));
```

https://github.com/code-423n4/2022-12-Stealth-Project/tree/main/router-v1/contracts/libraries/TransferHelper.sol#L43




#### <ins>Recommended Mitigation Steps</ins>

In addition to the zero-address checks, add a check to verify that `<address>.code.length > 0`



### <a href="#Summary">[LOW&#x2011;3]</a><a name="LOW&#x2011;3"> Contracts are not using their OZ Upgradeable counterparts

The non-upgradeable standard version of OpenZeppelin’s library are inherited / used by the contracts.
It would be safer to use the upgradeable versions of the library contracts to avoid unexpected behaviour.

#### <ins>Proof of Concept</ins>

```solidity
3: import "@openzeppelin/contracts/token/ERC20/IERC20.sol";

```

https://github.com/code-423n4/2022-12-Stealth-Project/tree/main/maverick-v1/contracts/interfaces/IFactory.sol#L3

```solidity
4: import "@openzeppelin/contracts/token/ERC20/IERC20.sol";

```

https://github.com/code-423n4/2022-12-Stealth-Project/tree/main/maverick-v1/contracts/interfaces/IPool.sol#L4

```solidity
4: import "@openzeppelin/contracts/token/ERC721/extensions/IERC721Enumerable.sol";

```

https://github.com/code-423n4/2022-12-Stealth-Project/tree/main/maverick-v1/contracts/interfaces/IPosition.sol#L4

```solidity
4: import "@openzeppelin/contracts/token/ERC20/IERC20.sol";

```

https://github.com/code-423n4/2022-12-Stealth-Project/tree/main/maverick-v1/contracts/libraries/Deployer.sol#L4

```solidity
5: import "@openzeppelin/contracts/token/ERC20/IERC20.sol";
6: import "@openzeppelin/contracts/utils/Address.sol";

```

https://github.com/code-423n4/2022-12-Stealth-Project/tree/main/maverick-v1/contracts/libraries/SafeERC20Min.sol#L5-L6

```solidity
4: import "@openzeppelin/contracts/token/ERC20/extensions/IERC20Metadata.sol";
5: import "@openzeppelin/contracts/token/ERC20/IERC20.sol";

```

https://github.com/code-423n4/2022-12-Stealth-Project/tree/main/maverick-v1/contracts/models/Factory.sol#L4-L5

```solidity
4: import "@openzeppelin/contracts/token/ERC20/IERC20.sol";

```

https://github.com/code-423n4/2022-12-Stealth-Project/tree/main/maverick-v1/contracts/models/Pool.sol#L4

```solidity
4: import "@openzeppelin/contracts/token/ERC721/ERC721.sol";
5: import "@openzeppelin/contracts/token/ERC721/extensions/ERC721Enumerable.sol";
6: import "@openzeppelin/contracts/utils/Counters.sol";
7: import "@openzeppelin/contracts/access/Ownable.sol";

```

https://github.com/code-423n4/2022-12-Stealth-Project/tree/main/maverick-v1/contracts/models/Position.sol#L4-L7

```solidity
5: import "@openzeppelin/contracts/utils/Strings.sol";
6: import "@openzeppelin/contracts/access/Ownable.sol";

```

https://github.com/code-423n4/2022-12-Stealth-Project/tree/main/maverick-v1/contracts/models/PositionMetadata.sol#L5-L6

```solidity
4: import "@openzeppelin/contracts/token/ERC20/IERC20.sol";

```

https://github.com/code-423n4/2022-12-Stealth-Project/tree/main/router-v1/contracts/Router.sol#L4

```solidity
3: import "@openzeppelin/contracts/token/ERC20/IERC20.sol";

```

https://github.com/code-423n4/2022-12-Stealth-Project/tree/main/router-v1/contracts/interfaces/IRouter.sol#L3

```solidity
4: import "@openzeppelin/contracts/token/ERC20/IERC20.sol";

```

https://github.com/code-423n4/2022-12-Stealth-Project/tree/main/router-v1/contracts/interfaces/external/IWETH9.sol#L4

```solidity
4: import "@openzeppelin/contracts/token/ERC20/IERC20.sol";

```

https://github.com/code-423n4/2022-12-Stealth-Project/tree/main/router-v1/contracts/libraries/Path.sol#L4

```solidity
4: import "@openzeppelin/contracts/token/ERC20/IERC20.sol";
5: import "@openzeppelin/contracts/token/ERC20/extensions/draft-IERC20Permit.sol";

```

https://github.com/code-423n4/2022-12-Stealth-Project/tree/main/router-v1/contracts/libraries/SelfPermit.sol#L4-L5

```solidity
4: import "@openzeppelin/contracts/token/ERC20/IERC20.sol";

```

https://github.com/code-423n4/2022-12-Stealth-Project/tree/main/router-v1/contracts/libraries/TransferHelper.sol#L4



#### <ins>Recommended Mitigation Steps</ins>

Where applicable, use the contracts from `@openzeppelin/contracts-upgradeable` instead of `@openzeppelin/contracts`.



### <a href="#Summary">[LOW&#x2011;4]</a><a name="LOW&#x2011;4"> Low Level Calls With Solidity Version Before 0.8.14 Can Result In Optimiser Bug

The project contracts in scope are using low level calls with solidity version before 0.8.14 which can result in optimizer bug.
https://medium.com/certora/overly-optimistic-optimizer-certora-bug-disclosure-2101e3f7994d

Simliar findings in Code4rena contests for reference:
https://code4rena.com/reports/2022-06-illuminate/#5-low-level-calls-with-solidity-version-0814-can-result-in-optimiser-bug

#### <ins>Proof Of Concept</ins>

POC can be found in the above medium reference url.

Functions that execute low level calls in contracts with solidity version under 0.8.14

```solidity
39: assembly {
        mstore(bins, sub(mload(bins), binCountToRemove))
    }
```

https://github.com/code-423n4/2022-12-Stealth-Project/tree/main/maverick-v1/contracts/models/PoolInspector.sol#L39

```solidity
89: assembly {
        mstore(bins, sub(mload(bins), binCounter))
    }
```

https://github.com/code-423n4/2022-12-Stealth-Project/tree/main/maverick-v1/contracts/models/PoolInspector.sol#L89

```solidity
19: assembly {
        switch iszero(_length)
        case 0 {
            tempBytes := mload(0x40)
            let lengthmod := and(_length, 31)
            let mc := add(add(tempBytes, lengthmod), mul(0x20, iszero(lengthmod)))
            let end := add(mc, _length)

            for {
                let cc := add(add(add(_bytes, lengthmod), mul(0x20, iszero(lengthmod))), _start)
            } lt(mc, end) {
                mc := add(mc, 0x20)
                cc := add(cc, 0x20)
            } {
                mstore(mc, mload(cc))
            }
            mstore(tempBytes, _length)
            mstore(0x40, and(add(mc, 31), not(31)))
        }
        default {
            tempBytes := mload(0x40)
            mstore(tempBytes, 0)

            mstore(0x40, add(tempBytes, 0x20))
        }
    }
```

https://github.com/code-423n4/2022-12-Stealth-Project/tree/main/router-v1/contracts/libraries/BytesLib.sol#L19





#### <ins>Recommended Mitigation Steps</ins>

Consider upgrading to at least solidity v0.8.15.



### <a href="#Summary">[LOW&#x2011;5]</a><a name="LOW&#x2011;5"> Upgrade OpenZeppelin Contract Dependency

An outdated OZ version is used (which has known vulnerabilities, see: https://github.com/OpenZeppelin/openzeppelin-contracts/security/advisories).

#### <ins>Proof Of Concept</ins>

Require dependencies to be at least version of 4.7.3 to include critical vulnerability patches.

```solidity
    "@openzeppelin/contracts": "^4.6.0"
```

https://github.com/code-423n4/2022-12-Stealth-Project/tree/main/../2022-11-chainlink/package.json#L27



#### <ins>Recommended Mitigation Steps</ins>

Update OpenZeppelin Contracts Usage in package.json and require at least version of 4.7.3 via `>=4.7.3`



## Non Critical Issues

### <a href="#Summary">[NC&#x2011;1]</a><a name="NC&#x2011;1"> Critical Changes Should Use Two-step Procedure

The critical procedures should be two step process.

See similar findings in previous Code4rena contests for reference:
https://code4rena.com/reports/2022-06-illuminate/#2-critical-changes-should-use-two-step-procedure

#### <ins>Proof Of Concept</ins>

```solidity
33: function setProtocolFeeRatio(uint16 _protocolFeeRatio) external {
```

https://github.com/code-423n4/2022-12-Stealth-Project/tree/main/maverick-v1/contracts/models/Factory.sol#L33

```solidity
40: function setOwner(address _owner) external {
```

https://github.com/code-423n4/2022-12-Stealth-Project/tree/main/maverick-v1/contracts/models/Factory.sol#L40

```solidity
332: function setProtocolFeeRatio(uint16 _protocolFeeRatio) internal {
```

https://github.com/code-423n4/2022-12-Stealth-Project/tree/main/maverick-v1/contracts/models/Pool.sol#L332

```solidity
23: function setMetadata(IPositionMetadata _metadata) external onlyOwner {
```

https://github.com/code-423n4/2022-12-Stealth-Project/tree/main/maverick-v1/contracts/models/Position.sol#L23

```solidity
17: function setBaseURI(string memory _baseURI) external onlyOwner {
```

https://github.com/code-423n4/2022-12-Stealth-Project/tree/main/maverick-v1/contracts/models/PositionMetadata.sol#L17



#### <ins>Recommended Mitigation Steps</ins>

Lack of two-step procedure for critical operations leaves them error-prone. Consider adding two step procedure on the critical functions.




### <a href="#Summary">[NC&#x2011;2]</a><a name="NC&#x2011;2"> Use a more recent version of Solidity

<a href="https://blog.soliditylang.org/2021/04/21/solidity-0.8.4-release-announcement/">0.8.4</a>:
bytes.concat() instead of abi.encodePacked(<bytes>,<bytes>)

<a href="https://blog.soliditylang.org/2022/02/16/solidity-0.8.12-release-announcement/">0.8.12</a>: 
string.concat() instead of abi.encodePacked(<str>,<str>)

<a href="https://blog.soliditylang.org/2022/03/16/solidity-0.8.13-release-announcement/">0.8.13</a>: 
Ability to use using for with a list of free functions

<a href="https://blog.soliditylang.org/2022/05/18/solidity-0.8.14-release-announcement/">0.8.14</a>:

ABI Encoder: When ABI-encoding values from calldata that contain nested arrays, correctly validate the nested array length against calldatasize() in all cases.
Override Checker: Allow changing data location for parameters only when overriding external functions.

<a href="https://blog.soliditylang.org/2022/06/15/solidity-0.8.15-release-announcement/">0.8.15</a>:

Code Generation: Avoid writing dirty bytes to storage when copying bytes arrays.
Yul Optimizer: Keep all memory side-effects of inline assembly blocks.

<a href="https://blog.soliditylang.org/2022/08/08/solidity-0.8.16-release-announcement/">0.8.16</a>:

Code Generation: Fix data corruption that affected ABI-encoding of calldata values represented by tuples: structs at any nesting level; argument lists of external functions, events and errors; return value lists of external functions. The 32 leading bytes of the first dynamically-encoded value in the tuple would get zeroed when the last component contained a statically-encoded array.

<a href="https://blog.soliditylang.org/2022/09/08/solidity-0.8.17-release-announcement/">0.8.17</a>:

Yul Optimizer: Prevent the incorrect removal of storage writes before calls to Yul functions that conditionally terminate the external EVM call.

#### <ins>Proof Of Concept</ins>


```solidity
pragma solidity ^0.8.0;
```

https://github.com/code-423n4/2022-12-Stealth-Project/tree/main/maverick-v1/contracts/interfaces/IAddLiquidityCallback.sol#L2

```solidity
pragma solidity ^0.8.0;
```

https://github.com/code-423n4/2022-12-Stealth-Project/tree/main/maverick-v1/contracts/interfaces/IFactory.sol#L2

```solidity
pragma solidity ^0.8.0;
```

https://github.com/code-423n4/2022-12-Stealth-Project/tree/main/maverick-v1/contracts/interfaces/IPool.sol#L2

```solidity
pragma solidity ^0.8.0;
```

https://github.com/code-423n4/2022-12-Stealth-Project/tree/main/maverick-v1/contracts/interfaces/IPoolAdmin.sol#L2

```solidity
pragma solidity ^0.8.0;
```

https://github.com/code-423n4/2022-12-Stealth-Project/tree/main/maverick-v1/contracts/interfaces/IPosition.sol#L2

```solidity
pragma solidity ^0.8.0;
```

https://github.com/code-423n4/2022-12-Stealth-Project/tree/main/maverick-v1/contracts/interfaces/IPositionMetadata.sol#L2

```solidity
pragma solidity ^0.8.0;
```

https://github.com/code-423n4/2022-12-Stealth-Project/tree/main/maverick-v1/contracts/interfaces/ISwapCallback.sol#L2

```solidity
pragma solidity ^0.8.0;
```

https://github.com/code-423n4/2022-12-Stealth-Project/tree/main/maverick-v1/contracts/libraries/Bin.sol#L2

```solidity
pragma solidity ^0.8.0;
```

https://github.com/code-423n4/2022-12-Stealth-Project/tree/main/maverick-v1/contracts/libraries/BinMap.sol#L2

```solidity
pragma solidity ^0.8.0;
```

https://github.com/code-423n4/2022-12-Stealth-Project/tree/main/maverick-v1/contracts/libraries/BinMath.sol#L2

```solidity
pragma solidity ^0.8.0;
```

https://github.com/code-423n4/2022-12-Stealth-Project/tree/main/maverick-v1/contracts/libraries/Cast.sol#L2

```solidity
pragma solidity ^0.8.0;
```

https://github.com/code-423n4/2022-12-Stealth-Project/tree/main/maverick-v1/contracts/libraries/Constants.sol#L2

```solidity
pragma solidity ^0.8.0;
```

https://github.com/code-423n4/2022-12-Stealth-Project/tree/main/maverick-v1/contracts/libraries/Delta.sol#L2

```solidity
pragma solidity ^0.8.0;
```

https://github.com/code-423n4/2022-12-Stealth-Project/tree/main/maverick-v1/contracts/libraries/Deployer.sol#L2

```solidity
pragma solidity ^0.8.0;
```

https://github.com/code-423n4/2022-12-Stealth-Project/tree/main/maverick-v1/contracts/libraries/Math.sol#L2

```solidity
pragma solidity ^0.8.0;
```

https://github.com/code-423n4/2022-12-Stealth-Project/tree/main/maverick-v1/contracts/libraries/SafeERC20Min.sol#L3

```solidity
pragma solidity ^0.8.0;
```

https://github.com/code-423n4/2022-12-Stealth-Project/tree/main/maverick-v1/contracts/libraries/Twa.sol#L2

```solidity
pragma solidity ^0.8.0;
```

https://github.com/code-423n4/2022-12-Stealth-Project/tree/main/maverick-v1/contracts/models/Factory.sol#L2

```solidity
pragma solidity ^0.8.0;
```

https://github.com/code-423n4/2022-12-Stealth-Project/tree/main/maverick-v1/contracts/models/Pool.sol#L2

```solidity
pragma solidity ^0.8.0;
```

https://github.com/code-423n4/2022-12-Stealth-Project/tree/main/maverick-v1/contracts/models/PoolInspector.sol#L2

```solidity
pragma solidity ^0.8.4;
```

https://github.com/code-423n4/2022-12-Stealth-Project/tree/main/maverick-v1/contracts/models/Position.sol#L2

```solidity
pragma solidity ^0.8.4;
```

https://github.com/code-423n4/2022-12-Stealth-Project/tree/main/maverick-v1/contracts/models/PositionMetadata.sol#L2

```solidity
pragma solidity ^0.8.0;
```

https://github.com/code-423n4/2022-12-Stealth-Project/tree/main/router-v1/contracts/Router.sol#L2

```solidity
pragma solidity >=0.7.5;
```

https://github.com/code-423n4/2022-12-Stealth-Project/tree/main/router-v1/contracts/interfaces/IMulticall.sol#L2

```solidity
pragma solidity ^0.8.0;
```

https://github.com/code-423n4/2022-12-Stealth-Project/tree/main/router-v1/contracts/interfaces/IRouter.sol#L2

```solidity
pragma solidity >=0.7.5;
```

https://github.com/code-423n4/2022-12-Stealth-Project/tree/main/router-v1/contracts/interfaces/ISelfPermit.sol#L2

```solidity
pragma solidity >=0.5.0;
```

https://github.com/code-423n4/2022-12-Stealth-Project/tree/main/router-v1/contracts/interfaces/external/IERC20PermitAllowed.sol#L2

```solidity
pragma solidity ^0.8.0;
```

https://github.com/code-423n4/2022-12-Stealth-Project/tree/main/router-v1/contracts/interfaces/external/IWETH9.sol#L2

```solidity
pragma solidity ^0.8.0;
```

https://github.com/code-423n4/2022-12-Stealth-Project/tree/main/router-v1/contracts/libraries/BytesLib.sol#L9

```solidity
pragma solidity ^0.8.0;
```

https://github.com/code-423n4/2022-12-Stealth-Project/tree/main/router-v1/contracts/libraries/Deadline.sol#L2

```solidity
pragma solidity ^0.8.0;
```

https://github.com/code-423n4/2022-12-Stealth-Project/tree/main/router-v1/contracts/libraries/Multicall.sol#L2

```solidity
pragma solidity ^0.8.0;
```

https://github.com/code-423n4/2022-12-Stealth-Project/tree/main/router-v1/contracts/libraries/Path.sol#L2

```solidity
pragma solidity >=0.5.0;
```

https://github.com/code-423n4/2022-12-Stealth-Project/tree/main/router-v1/contracts/libraries/SelfPermit.sol#L2

```solidity
pragma solidity >=0.6.0;
```

https://github.com/code-423n4/2022-12-Stealth-Project/tree/main/router-v1/contracts/libraries/TransferHelper.sol#L2



#### <ins>Recommended Mitigation Steps</ins>

Consider updating to a more recent solidity version.





### <a href="#Summary">[NC&#x2011;3]</a><a name="NC&#x2011;3"> Event Is Missing Indexed Fields

Index event fields make the field more quickly accessible to off-chain tools that parse events. However, note that each index field costs extra gas during emission, so it's not necessarily best to index the maximum allowed per event (three fields). 

Each event should use three indexed fields if there are three or more fields, and gas usage is not particularly of concern for the events in question. If there are fewer than three fields, all of the fields should be indexed.

#### <ins>Proof Of Concept</ins>


```solidity
event PoolCreated(address poolAddress, uint256 fee, uint256 tickSpacing, int32 activeTick, uint32 lookback, uint16 protocolFeeRatio, IERC20 tokenA, IERC20 tokenB);
```

https://github.com/code-423n4/2022-12-Stealth-Project/tree/main/maverick-v1/contracts/interfaces/IFactory.sol#L9

```solidity
event TransferLiquidity(uint256 fromTokenId, uint256 toTokenId, RemoveLiquidityParams[] params);
```

https://github.com/code-423n4/2022-12-Stealth-Project/tree/main/maverick-v1/contracts/interfaces/IPool.sol#L14

```solidity
event ProtocolFeeCollected(uint256 protocolFee, bool isTokenA);
```

https://github.com/code-423n4/2022-12-Stealth-Project/tree/main/maverick-v1/contracts/interfaces/IPool.sol#L22






### <a href="#Summary">[NC&#x2011;4]</a><a name="NC&#x2011;4"> Missing event for critical parameter change

When changing state variables events are not emitted. Emitting events allows monitoring activities with off-chain monitoring tools.

#### <ins>Proof Of Concept</ins>


```solidity
17: function setBaseURI(string memory _baseURI) external onlyOwner {
```

https://github.com/code-423n4/2022-12-Stealth-Project/tree/main/maverick-v1/contracts/models/PositionMetadata.sol#L17








### <a href="#Summary">[NC&#x2011;5]</a><a name="NC&#x2011;5"> Implementation contract may not be initialized

OpenZeppelin recommends that the initializer modifier be applied to constructors. 
Per OZs Post implementation contract should be initialized to avoid potential griefs or exploits.
https://forum.openzeppelin.com/t/uupsupgradeable-vulnerability-post-mortem/15680/5

#### <ins>Proof Of Concept</ins>


```solidity
26: constructor(uint16 _protocolFeeRatio, IPosition _position)
```

https://github.com/code-423n4/2022-12-Stealth-Project/tree/main/maverick-v1/contracts/models/Factory.sol#L26

```solidity
60: constructor(
        uint256 _fee,
        uint256 _tickSpacing,
        int32 _activeTick,
        uint16 _lookback,
        uint16 _protocolFeeRatio,
        IERC20 _tokenA,
        IERC20 _tokenB,
        uint256 _tokenAScale,
        uint256 _tokenBScale,
        IPosition _position
    )
```

https://github.com/code-423n4/2022-12-Stealth-Project/tree/main/maverick-v1/contracts/models/Pool.sol#L60

```solidity
18: constructor(IPositionMetadata _metadata) ERC721("Maverick Position NFT", "MPN")
```

https://github.com/code-423n4/2022-12-Stealth-Project/tree/main/maverick-v1/contracts/models/Position.sol#L18

```solidity
13: constructor(string memory _baseURI)
```

https://github.com/code-423n4/2022-12-Stealth-Project/tree/main/maverick-v1/contracts/models/PositionMetadata.sol#L13

```solidity
48: constructor(IFactory _factory, IPosition _position, IWETH9 _WETH9)
```

https://github.com/code-423n4/2022-12-Stealth-Project/tree/main/router-v1/contracts/Router.sol#L48





### <a href="#Summary">[NC&#x2011;6]</a><a name="NC&#x2011;6"> Use of Block.Timestamp

Block timestamps have historically been used for a variety of applications, such as entropy for random numbers (see the Entropy Illusion for further details), locking funds for periods of time, and various state-changing conditional statements that are time-dependent. Miners have the ability to adjust timestamps slightly, which can prove to be dangerous if block timestamps are used incorrectly in smart contracts.
References: SWC ID: 116

#### <ins>Proof Of Concept</ins>


```solidity
22: if (block.timestamp == self.lastTimestamp) {
```

https://github.com/code-423n4/2022-12-Stealth-Project/tree/main/maverick-v1/contracts/libraries/Twa.sol#L22


```solidity
6: require(block.timestamp <= deadline, "Transaction too old");
```

https://github.com/code-423n4/2022-12-Stealth-Project/tree/main/router-v1/contracts/libraries/Deadline.sol#L6



#### <ins>Recommended Mitigation Steps</ins>
Block timestamps should not be used for entropy or generating random numbers—i.e., they should not be the deciding factor (either directly or through some derivation) for winning a game or changing an important state.

Time-sensitive logic is sometimes required; e.g., for unlocking contracts (time-locking), completing an ICO after a few weeks, or enforcing expiry dates. It is sometimes recommended to use block.number and an average block time to estimate times; with a 10 second block time, 1 week equates to approximately, 60480 blocks. Thus, specifying a block number at which to change a contract state can be more secure, as miners are unable to easily manipulate the block number.



### <a href="#Summary">[NC&#x2011;7]</a><a name="NC&#x2011;7"> Non-usage of specific imports

The current form of relative path import is not recommended for use because it can unpredictably pollute the namespace.
Instead, the Solidity docs recommend specifying imported symbols explicitly.
https://docs.soliditylang.org/en/v0.8.15/layout-of-source-files.html#importing-other-source-files

A good example:
```solidity
import {OwnableUpgradeable} from "openzeppelin-contracts-upgradeable/contracts/access/OwnableUpgradeable.sol";
import {SafeTransferLib} from "solmate/utils/SafeTransferLib.sol";
import {SafeCastLib} from "solmate/utils/SafeCastLib.sol";
import {ERC20} from "solmate/tokens/ERC20.sol";
import {IProducer} from "src/interfaces/IProducer.sol";
import {GlobalState, UserState} from "src/Common.sol";
```

#### <ins>Proof Of Concept</ins>


```solidity
5: import "../interfaces/IPool.sol";
6: import "../interfaces/IPosition.sol";

```

https://github.com/code-423n4/2022-12-Stealth-Project/tree/main/maverick-v1/contracts/interfaces/IFactory.sol#L5-L6

```solidity
5: import "./IFactory.sol";

```

https://github.com/code-423n4/2022-12-Stealth-Project/tree/main/maverick-v1/contracts/interfaces/IPool.sol#L5

```solidity
5: import "../interfaces/IPositionMetadata.sol";

```

https://github.com/code-423n4/2022-12-Stealth-Project/tree/main/maverick-v1/contracts/interfaces/IPosition.sol#L5

```solidity
6: import "./Math.sol";
7: import "./BinMath.sol";
8: import "./Delta.sol";
9: import "./Cast.sol";
10: import "./Constants.sol";
11: import "../interfaces/IPool.sol";

```

https://github.com/code-423n4/2022-12-Stealth-Project/tree/main/maverick-v1/contracts/libraries/Bin.sol#L6-L11

```solidity
6: import "./BinMath.sol";
7: import "./Constants.sol";

```

https://github.com/code-423n4/2022-12-Stealth-Project/tree/main/maverick-v1/contracts/libraries/BinMap.sol#L6-L7

```solidity
6: import "./Math.sol";
7: import "./Constants.sol";

```

https://github.com/code-423n4/2022-12-Stealth-Project/tree/main/maverick-v1/contracts/libraries/BinMath.sol#L6-L7

```solidity
6: import "../models/Pool.sol";
7: import "../interfaces/IFactory.sol";
8: import "../interfaces/IPool.sol";
9: import "../interfaces/IPosition.sol";

```

https://github.com/code-423n4/2022-12-Stealth-Project/tree/main/maverick-v1/contracts/libraries/Deployer.sol#L6-L9

```solidity
5: import "./Constants.sol";

```

https://github.com/code-423n4/2022-12-Stealth-Project/tree/main/maverick-v1/contracts/libraries/Math.sol#L5

```solidity
6: import "../interfaces/IPool.sol";

```

https://github.com/code-423n4/2022-12-Stealth-Project/tree/main/maverick-v1/contracts/libraries/Twa.sol#L6

```solidity
7: import "../models/Pool.sol";
8: import "../interfaces/IFactory.sol";
9: import "../interfaces/IPool.sol";
10: import "../interfaces/IPosition.sol";
11: import "../libraries/Deployer.sol";
12: import "../libraries/Math.sol";

```

https://github.com/code-423n4/2022-12-Stealth-Project/tree/main/maverick-v1/contracts/models/Factory.sol#L7-L12

```solidity
8: import "../libraries/Bin.sol";
9: import "../libraries/BinMap.sol";
10: import "../libraries/BinMath.sol";
11: import "../libraries/Cast.sol";
12: import "../libraries/Delta.sol";
13: import "../libraries/Twa.sol";
14: import "../libraries/SafeERC20Min.sol";
15: import "../interfaces/ISwapCallback.sol";
16: import "../interfaces/IAddLiquidityCallback.sol";
17: import "../interfaces/IPool.sol";
18: import "../interfaces/IPoolAdmin.sol";
19: import "../interfaces/IFactory.sol";
20: import "../interfaces/IPosition.sol";

```

https://github.com/code-423n4/2022-12-Stealth-Project/tree/main/maverick-v1/contracts/models/Pool.sol#L8-L20

```solidity
4: import "./Pool.sol";
5: import "../interfaces/ISwapCallback.sol";
6: import "../libraries/Bin.sol";
7: import "../libraries/Math.sol";
8: import "../libraries/Constants.sol";
9: import "../interfaces/IPool.sol";

```

https://github.com/code-423n4/2022-12-Stealth-Project/tree/main/maverick-v1/contracts/models/PoolInspector.sol#L4-L9

```solidity
9: import "../interfaces/IPositionMetadata.sol";
10: import "../interfaces/IPosition.sol";

```

https://github.com/code-423n4/2022-12-Stealth-Project/tree/main/maverick-v1/contracts/models/Position.sol#L9-L10

```solidity
4: import "../interfaces/IPositionMetadata.sol";

```

https://github.com/code-423n4/2022-12-Stealth-Project/tree/main/maverick-v1/contracts/models/PositionMetadata.sol#L4

```solidity
10: import "./interfaces/IRouter.sol";
11: import "./interfaces/external/IWETH9.sol";
12: import "./libraries/TransferHelper.sol";
13: import "./libraries/Path.sol";
14: import "./libraries/Deadline.sol";
15: import "./libraries/Multicall.sol";
16: import "./libraries/SelfPermit.sol";

```

https://github.com/code-423n4/2022-12-Stealth-Project/tree/main/router-v1/contracts/Router.sol#L10-L16

```solidity
9: import "./external/IWETH9.sol";

```

https://github.com/code-423n4/2022-12-Stealth-Project/tree/main/router-v1/contracts/interfaces/IRouter.sol#L9

```solidity
5: import "../interfaces/IMulticall.sol";

```

https://github.com/code-423n4/2022-12-Stealth-Project/tree/main/router-v1/contracts/libraries/Multicall.sol#L5

```solidity
8: import "./BytesLib.sol";

```

https://github.com/code-423n4/2022-12-Stealth-Project/tree/main/router-v1/contracts/libraries/Path.sol#L8

```solidity
7: import "../interfaces/ISelfPermit.sol";
8: import "../interfaces/external/IERC20PermitAllowed.sol";

```

https://github.com/code-423n4/2022-12-Stealth-Project/tree/main/router-v1/contracts/libraries/SelfPermit.sol#L7-L8




#### <ins>Recommended Mitigation Steps</ins>

Use specific imports syntax per solidity docs recommendation.



### <a href="#Summary">[NC&#x2011;8]</a><a name="NC&#x2011;8"> Use `bytes.concat()`

Solidity version 0.8.4 introduces `bytes.concat()` (vs `abi.encodePacked(<bytes>,<bytes>)`)

#### <ins>Proof Of Concept</ins>


```solidity
22: return bytes(baseURI).length > 0 ? string(abi.encodePacked(baseURI, tokenId.toString()

```

https://github.com/code-423n4/2022-12-Stealth-Project/tree/main/maverick-v1/contracts/models/PositionMetadata.sol#L22

```solidity
137: SwapCallbackData({path: abi.encodePacked(params.tokenIn, params.pool, params.tokenOut)
185: SwapCallbackData({path: abi.encodePacked(params.tokenOut, params.pool, params.tokenIn)

```

https://github.com/code-423n4/2022-12-Stealth-Project/tree/main/router-v1/contracts/Router.sol#L137

https://github.com/code-423n4/2022-12-Stealth-Project/tree/main/router-v1/contracts/Router.sol#L185






#### <ins>Recommended Mitigation Steps</ins>

Use `bytes.concat()` and upgrade to at least Solidity version 0.8.4 if required. 



### <a href="#Summary">[NC&#x2011;9]</a><a name="NC&#x2011;9"> No need to initialize uints to zero

There is no need to initialize `uint` variables to zero as their default value is `0`

#### <ins>Proof Of Concept</ins>

```solidity
29: uint128 activeCounter = 0;

```

https://github.com/code-423n4/2022-12-Stealth-Project/tree/main/maverick-v1/contracts/models/PoolInspector.sol#L29





### <a href="#Summary">[NC&#x2011;10]</a><a name="NC&#x2011;10"> Use `delete` to Clear Variables

`delete a` assigns the initial value for the type to `a`. i.e. for integers it is equivalent to `a = 0`, but it can also be used on arrays, where it assigns a dynamic array of length zero or a static array of the same length with all elements reset. For structs, it assigns a struct with all members reset. Similarly, it can also be used to set an address to zero address. It has no effect on whole mappings though (as the keys of mappings may be arbitrary and are generally unknown). However, individual keys and what they map to can be deleted: If `a` is a mapping, then `delete a[x]` will delete the value stored at `x`.

The `delete` key better conveys the intention and is also more idiomatic. Consider replacing assignments of zero with `delete` statements.

#### <ins>Proof Of Concept</ins>


```solidity
359: binPositions[tick][kind] = 0;

```

https://github.com/code-423n4/2022-12-Stealth-Project/tree/main/maverick-v1/contracts/models/Pool.sol#L359







