## Summary

### Low Risk Issues
| |Issue|Instances|
|-|:-|:-:|
| [L&#x2011;01] | `tokenURI()` does not follow EIP-721 | 1 | 
| [L&#x2011;02] | Draft imports may break in new minor versions | 1 | 

Total: 2 instances over 2 issues


### Non-critical Issues
| |Issue|Instances|
|-|:-|:-:|
| [N&#x2011;01] | Import declarations should import specific identifiers, rather than the whole file | 97 | 
| [N&#x2011;02] | `constant`s should be defined rather than using magic numbers | 107 | 
| [N&#x2011;03] | Numeric values having to do with time should use time units for readability | 1 | 
| [N&#x2011;04] | Use bit shifts in an imutable variable rather than long bit masks of a single bit, for readability | 4 | 
| [N&#x2011;05] | Missing event and or timelock for critical parameter change | 1 | 
| [N&#x2011;06] | Events that mark critical parameter changes should contain both the old and the new value | 3 | 
| [N&#x2011;07] | Use a more recent version of solidity | 1 | 
| [N&#x2011;08] | Use a more recent version of solidity | 8 | 
| [N&#x2011;09] | Use a more recent version of solidity | 1 | 
| [N&#x2011;10] | Inconsistent spacing in comments | 28 | 
| [N&#x2011;11] | Lines are too long | 18 | 
| [N&#x2011;12] | Variable names that consist of all capital letters should be reserved for `constant`/`immutable` variables | 1 | 
| [N&#x2011;13] | Non-library/interface files should use fixed compiler versions, not floating ones | 6 | 
| [N&#x2011;14] | File is missing NatSpec | 18 | 
| [N&#x2011;15] | NatSpec is incomplete | 9 | 
| [N&#x2011;16] | Not using the named return variables anywhere in the function is confusing | 17 | 
| [N&#x2011;17] | Duplicated `require()`/`revert()` checks should be refactored to a modifier or function | 3 | 
| [N&#x2011;18] | Consider using `delete` rather than assigning zero to clear values | 6 | 
| [N&#x2011;19] | Contracts should have full test coverage | 1 | 
| [N&#x2011;20] | Large or complicated code bases should implement fuzzing tests | 1 | 

Total: 331 instances over 20 issues





## Low Risk Issues

### [L&#x2011;01]  `tokenURI()` does not follow EIP-721
The [EIP](https://eips.ethereum.org/EIPS/eip-721) states that `tokenURI()` "Throws if `_tokenId` is not a valid NFT", which the code below does not do. If the NFT has not yet been minted, `tokenURI()` should revert

*There is 1 instance of this issue:*

```solidity
File: maverick-v1/contracts/models/PositionMetadata.sol

21        function tokenURI(uint256 tokenId) external view override returns (string memory) {
22            return bytes(baseURI).length > 0 ? string(abi.encodePacked(baseURI, tokenId.toString())) : "";
23:       }

```
https://github.com/code-423n4/2022-12-Stealth-Project/blob/fc8589d7d8c1d8488fd97ccc46e1ff11c8426ac2/maverick-v1/contracts/models/PositionMetadata.sol#L21-L23

### [L&#x2011;02]  Draft imports may break in new minor versions
While OpenZeppelin draft contracts are safe to use and have been audited, their 'draft' status means that the EIPs they're based on are not finalized, and thus there may be breaking changes in even [minor releases](https://docs.openzeppelin.com/contracts/3.x/api/drafts). If a bug is found in this version of OpenZeppelin, and the version that you're forced to upgrade to has breaking changes in the new version, you'll encounter unnecessary delays in porting and testing replacement contracts. Ensure that you have extensive test coverage of this area so that differences can be automatically detected, and have a plan in place for how you would fully test a new version of these contracts if they do indeed change unexpectedly. Consider creating a forked version of the file rather than importing it from the package, and manually patch your fork as changes are made.

*There is 1 instance of this issue:*

```solidity
File: router-v1/contracts/libraries/SelfPermit.sol

5:    import "@openzeppelin/contracts/token/ERC20/extensions/draft-IERC20Permit.sol";

```
https://github.com/code-423n4/2022-12-Stealth-Project/blob/fc8589d7d8c1d8488fd97ccc46e1ff11c8426ac2/router-v1/contracts/libraries/SelfPermit.sol#L5

## Non-critical Issues

### [N&#x2011;01]  Import declarations should import specific identifiers, rather than the whole file
Using import declarations of the form `import {<identifier_name>} from "some/file.sol"` avoids polluting the symbol namespace making flattened files smaller, and speeds up compilation

*There are 97 instances of this issue:*

```solidity
File: maverick-v1/contracts/interfaces/IFactory.sol

3:    import "@openzeppelin/contracts/token/ERC20/IERC20.sol";

5:    import "../interfaces/IPool.sol";

6:    import "../interfaces/IPosition.sol";

```
https://github.com/code-423n4/2022-12-Stealth-Project/blob/fc8589d7d8c1d8488fd97ccc46e1ff11c8426ac2/maverick-v1/contracts/interfaces/IFactory.sol#L3

```solidity
File: maverick-v1/contracts/interfaces/IPool.sol

4:    import "@openzeppelin/contracts/token/ERC20/IERC20.sol";

5:    import "./IFactory.sol";

```
https://github.com/code-423n4/2022-12-Stealth-Project/blob/fc8589d7d8c1d8488fd97ccc46e1ff11c8426ac2/maverick-v1/contracts/interfaces/IPool.sol#L4

```solidity
File: maverick-v1/contracts/interfaces/IPosition.sol

4:    import "@openzeppelin/contracts/token/ERC721/extensions/IERC721Enumerable.sol";

5:    import "../interfaces/IPositionMetadata.sol";

```
https://github.com/code-423n4/2022-12-Stealth-Project/blob/fc8589d7d8c1d8488fd97ccc46e1ff11c8426ac2/maverick-v1/contracts/interfaces/IPosition.sol#L4

```solidity
File: maverick-v1/contracts/libraries/BinMap.sol

4:    import "prb-math/contracts/PRBMathSD59x18.sol";

6:    import "./BinMath.sol";

7:    import "./Constants.sol";

```
https://github.com/code-423n4/2022-12-Stealth-Project/blob/fc8589d7d8c1d8488fd97ccc46e1ff11c8426ac2/maverick-v1/contracts/libraries/BinMap.sol#L4

```solidity
File: maverick-v1/contracts/libraries/BinMath.sol

4:    import "prb-math/contracts/PRBMathUD60x18.sol";

6:    import "./Math.sol";

7:    import "./Constants.sol";

```
https://github.com/code-423n4/2022-12-Stealth-Project/blob/fc8589d7d8c1d8488fd97ccc46e1ff11c8426ac2/maverick-v1/contracts/libraries/BinMath.sol#L4

```solidity
File: maverick-v1/contracts/libraries/Bin.sol

4:    import "prb-math/contracts/PRBMathUD60x18.sol";

6:    import "./Math.sol";

7:    import "./BinMath.sol";

8:    import "./Delta.sol";

9:    import "./Cast.sol";

10:   import "./Constants.sol";

11:   import "../interfaces/IPool.sol";

```
https://github.com/code-423n4/2022-12-Stealth-Project/blob/fc8589d7d8c1d8488fd97ccc46e1ff11c8426ac2/maverick-v1/contracts/libraries/Bin.sol#L4

```solidity
File: maverick-v1/contracts/libraries/Deployer.sol

4:    import "@openzeppelin/contracts/token/ERC20/IERC20.sol";

6:    import "../models/Pool.sol";

7:    import "../interfaces/IFactory.sol";

8:    import "../interfaces/IPool.sol";

9:    import "../interfaces/IPosition.sol";

```
https://github.com/code-423n4/2022-12-Stealth-Project/blob/fc8589d7d8c1d8488fd97ccc46e1ff11c8426ac2/maverick-v1/contracts/libraries/Deployer.sol#L4

```solidity
File: maverick-v1/contracts/libraries/Math.sol

3:    import "prb-math/contracts/PRBMath.sol";

5:    import "./Constants.sol";

```
https://github.com/code-423n4/2022-12-Stealth-Project/blob/fc8589d7d8c1d8488fd97ccc46e1ff11c8426ac2/maverick-v1/contracts/libraries/Math.sol#L3

```solidity
File: maverick-v1/contracts/libraries/SafeERC20Min.sol

5:    import "@openzeppelin/contracts/token/ERC20/IERC20.sol";

6:    import "@openzeppelin/contracts/utils/Address.sol";

```
https://github.com/code-423n4/2022-12-Stealth-Project/blob/fc8589d7d8c1d8488fd97ccc46e1ff11c8426ac2/maverick-v1/contracts/libraries/SafeERC20Min.sol#L5

```solidity
File: maverick-v1/contracts/libraries/Twa.sol

4:    import "prb-math/contracts/PRBMathSD59x18.sol";

6:    import "../interfaces/IPool.sol";

```
https://github.com/code-423n4/2022-12-Stealth-Project/blob/fc8589d7d8c1d8488fd97ccc46e1ff11c8426ac2/maverick-v1/contracts/libraries/Twa.sol#L4

```solidity
File: maverick-v1/contracts/models/Factory.sol

4:    import "@openzeppelin/contracts/token/ERC20/extensions/IERC20Metadata.sol";

5:    import "@openzeppelin/contracts/token/ERC20/IERC20.sol";

7:    import "../models/Pool.sol";

8:    import "../interfaces/IFactory.sol";

9:    import "../interfaces/IPool.sol";

10:   import "../interfaces/IPosition.sol";

11:   import "../libraries/Deployer.sol";

12:   import "../libraries/Math.sol";

```
https://github.com/code-423n4/2022-12-Stealth-Project/blob/fc8589d7d8c1d8488fd97ccc46e1ff11c8426ac2/maverick-v1/contracts/models/Factory.sol#L4

```solidity
File: maverick-v1/contracts/models/PoolInspector.sol

4:    import "./Pool.sol";

5:    import "../interfaces/ISwapCallback.sol";

6:    import "../libraries/Bin.sol";

7:    import "../libraries/Math.sol";

8:    import "../libraries/Constants.sol";

9:    import "../interfaces/IPool.sol";

```
https://github.com/code-423n4/2022-12-Stealth-Project/blob/fc8589d7d8c1d8488fd97ccc46e1ff11c8426ac2/maverick-v1/contracts/models/PoolInspector.sol#L4

```solidity
File: maverick-v1/contracts/models/Pool.sol

4:    import "@openzeppelin/contracts/token/ERC20/IERC20.sol";

6:    import "prb-math/contracts/PRBMathUD60x18.sol";

7:    import "prb-math/contracts/PRBMathSD59x18.sol";

8:    import "../libraries/Bin.sol";

9:    import "../libraries/BinMap.sol";

10:   import "../libraries/BinMath.sol";

11:   import "../libraries/Cast.sol";

12:   import "../libraries/Delta.sol";

13:   import "../libraries/Twa.sol";

14:   import "../libraries/SafeERC20Min.sol";

15:   import "../interfaces/ISwapCallback.sol";

16:   import "../interfaces/IAddLiquidityCallback.sol";

17:   import "../interfaces/IPool.sol";

18:   import "../interfaces/IPoolAdmin.sol";

19:   import "../interfaces/IFactory.sol";

20:   import "../interfaces/IPosition.sol";

```
https://github.com/code-423n4/2022-12-Stealth-Project/blob/fc8589d7d8c1d8488fd97ccc46e1ff11c8426ac2/maverick-v1/contracts/models/Pool.sol#L4

```solidity
File: maverick-v1/contracts/models/PositionMetadata.sol

4:    import "../interfaces/IPositionMetadata.sol";

5:    import "@openzeppelin/contracts/utils/Strings.sol";

6:    import "@openzeppelin/contracts/access/Ownable.sol";

```
https://github.com/code-423n4/2022-12-Stealth-Project/blob/fc8589d7d8c1d8488fd97ccc46e1ff11c8426ac2/maverick-v1/contracts/models/PositionMetadata.sol#L4

```solidity
File: maverick-v1/contracts/models/Position.sol

4:    import "@openzeppelin/contracts/token/ERC721/ERC721.sol";

5:    import "@openzeppelin/contracts/token/ERC721/extensions/ERC721Enumerable.sol";

6:    import "@openzeppelin/contracts/utils/Counters.sol";

7:    import "@openzeppelin/contracts/access/Ownable.sol";

9:    import "../interfaces/IPositionMetadata.sol";

10:   import "../interfaces/IPosition.sol";

```
https://github.com/code-423n4/2022-12-Stealth-Project/blob/fc8589d7d8c1d8488fd97ccc46e1ff11c8426ac2/maverick-v1/contracts/models/Position.sol#L4

```solidity
File: router-v1/contracts/interfaces/external/IWETH9.sol

4:    import "@openzeppelin/contracts/token/ERC20/IERC20.sol";

```
https://github.com/code-423n4/2022-12-Stealth-Project/blob/fc8589d7d8c1d8488fd97ccc46e1ff11c8426ac2/router-v1/contracts/interfaces/external/IWETH9.sol#L4

```solidity
File: router-v1/contracts/interfaces/IRouter.sol

3:    import "@openzeppelin/contracts/token/ERC20/IERC20.sol";

5:    import "@maverick/contracts/contracts/interfaces/IFactory.sol";

6:    import "@maverick/contracts/contracts/interfaces/IPool.sol";

7:    import "@maverick/contracts/contracts/interfaces/IPosition.sol";

8:    import "@maverick/contracts/contracts/interfaces/ISwapCallback.sol";

9:    import "./external/IWETH9.sol";

```
https://github.com/code-423n4/2022-12-Stealth-Project/blob/fc8589d7d8c1d8488fd97ccc46e1ff11c8426ac2/router-v1/contracts/interfaces/IRouter.sol#L3

```solidity
File: router-v1/contracts/libraries/Multicall.sol

5:    import "../interfaces/IMulticall.sol";

```
https://github.com/code-423n4/2022-12-Stealth-Project/blob/fc8589d7d8c1d8488fd97ccc46e1ff11c8426ac2/router-v1/contracts/libraries/Multicall.sol#L5

```solidity
File: router-v1/contracts/libraries/Path.sol

4:    import "@openzeppelin/contracts/token/ERC20/IERC20.sol";

6:    import "@maverick/contracts/contracts/interfaces/IPool.sol";

8:    import "./BytesLib.sol";

```
https://github.com/code-423n4/2022-12-Stealth-Project/blob/fc8589d7d8c1d8488fd97ccc46e1ff11c8426ac2/router-v1/contracts/libraries/Path.sol#L4

```solidity
File: router-v1/contracts/libraries/SelfPermit.sol

4:    import "@openzeppelin/contracts/token/ERC20/IERC20.sol";

5:    import "@openzeppelin/contracts/token/ERC20/extensions/draft-IERC20Permit.sol";

7:    import "../interfaces/ISelfPermit.sol";

8:    import "../interfaces/external/IERC20PermitAllowed.sol";

```
https://github.com/code-423n4/2022-12-Stealth-Project/blob/fc8589d7d8c1d8488fd97ccc46e1ff11c8426ac2/router-v1/contracts/libraries/SelfPermit.sol#L4

```solidity
File: router-v1/contracts/libraries/TransferHelper.sol

4:    import "@openzeppelin/contracts/token/ERC20/IERC20.sol";

```
https://github.com/code-423n4/2022-12-Stealth-Project/blob/fc8589d7d8c1d8488fd97ccc46e1ff11c8426ac2/router-v1/contracts/libraries/TransferHelper.sol#L4

```solidity
File: router-v1/contracts/Router.sol

4:    import "@openzeppelin/contracts/token/ERC20/IERC20.sol";

6:    import "@maverick/contracts/contracts/interfaces/IPool.sol";

7:    import "@maverick/contracts/contracts/interfaces/IFactory.sol";

8:    import "@maverick/contracts/contracts/interfaces/IPosition.sol";

10:   import "./interfaces/IRouter.sol";

11:   import "./interfaces/external/IWETH9.sol";

12:   import "./libraries/TransferHelper.sol";

13:   import "./libraries/Path.sol";

14:   import "./libraries/Deadline.sol";

15:   import "./libraries/Multicall.sol";

16:   import "./libraries/SelfPermit.sol";

```
https://github.com/code-423n4/2022-12-Stealth-Project/blob/fc8589d7d8c1d8488fd97ccc46e1ff11c8426ac2/router-v1/contracts/Router.sol#L4

### [N&#x2011;02]  `constant`s should be defined rather than using magic numbers
Even [assembly](https://github.com/code-423n4/2022-05-opensea-seaport/blob/9d7ce4d08bf3c3010304a0476a785c70c0e90ae7/contracts/lib/TokenTransferrer.sol#L35-L39) can benefit from using readable constants instead of hex/numeric literals

*There are 107 instances of this issue:*

```solidity
File: maverick-v1/contracts/libraries/BinMap.sol

/// @audit 8
22:           mapIndex = tick_kinds >> 8;

```
https://github.com/code-423n4/2022-12-Stealth-Project/blob/fc8589d7d8c1d8488fd97ccc46e1ff11c8426ac2/maverick-v1/contracts/libraries/BinMap.sol#L22

```solidity
File: maverick-v1/contracts/libraries/BinMath.sol

/// @audit 0x100000000000000000000000000000000
16:               if (x >= 0x100000000000000000000000000000000) {

/// @audit 128
17:                   x >>= 128;

/// @audit 128
18:                   result += 128;

/// @audit 0x10000000000000000
20:               if (x >= 0x10000000000000000) {

/// @audit 64
21:                   x >>= 64;

/// @audit 64
22:                   result += 64;

/// @audit 0x100000000
24:               if (x >= 0x100000000) {

/// @audit 32
25:                   x >>= 32;

/// @audit 32
26:                   result += 32;

/// @audit 0x10000
28:               if (x >= 0x10000) {

/// @audit 16
29:                   x >>= 16;

/// @audit 16
30:                   result += 16;

/// @audit 0x100
32:               if (x >= 0x100) {

/// @audit 8
33:                   x >>= 8;

/// @audit 8
34:                   result += 8;

/// @audit 0x10
36:               if (x >= 0x10) {

/// @audit 4
37:                   x >>= 4;

/// @audit 4
38:                   result += 4;

/// @audit 0x4
40:               if (x >= 0x4) {

/// @audit 0x2
44:               if (x >= 0x2) result += 1;

/// @audit 255
50:               result = 255;

/// @audit 0xFFFFFFFFFFFFFFFFFFFFFFFFFFFFFFFF
52:               if (x & 0xFFFFFFFFFFFFFFFFFFFFFFFFFFFFFFFF > 0) {

/// @audit 128
53:                   result -= 128;

/// @audit 128
55:                   x >>= 128;

/// @audit 0xFFFFFFFFFFFFFFFF
57:               if (x & 0xFFFFFFFFFFFFFFFF > 0) {

/// @audit 64
58:                   result -= 64;

/// @audit 64
60:                   x >>= 64;

/// @audit 0xFFFFFFFF
62:               if (x & 0xFFFFFFFF > 0) {

/// @audit 32
63:                   result -= 32;

/// @audit 32
65:                   x >>= 32;

/// @audit 0xFFFF
67:               if (x & 0xFFFF > 0) {

/// @audit 16
68:                   result -= 16;

/// @audit 16
70:                   x >>= 16;

/// @audit 0xFF
72:               if (x & 0xFF > 0) {

/// @audit 8
73:                   result -= 8;

/// @audit 8
75:                   x >>= 8;

/// @audit 0xF
77:               if (x & 0xF > 0) {

/// @audit 4
78:                   result -= 4;

/// @audit 4
80:                   x >>= 4;

/// @audit 0x3
82:               if (x & 0x3 > 0) {

/// @audit 0x1
87:               if (x & 0x1 > 0) result -= 1;

/// @audit 0x1
/// @audit 0xfffcb933bd6fad9d3af5f0b9f25db4d6
/// @audit 0x100000000000000000000000000000000
96:           uint256 ratio = tick & 0x1 != 0 ? 0xfffcb933bd6fad9d3af5f0b9f25db4d6 : 0x100000000000000000000000000000000;

/// @audit 0x2
/// @audit 0xfff97272373d41fd789c8cb37ffcaa1c
/// @audit 128
97:           if (tick & 0x2 != 0) ratio = (ratio * 0xfff97272373d41fd789c8cb37ffcaa1c) >> 128;

/// @audit 0x4
/// @audit 0xfff2e50f5f656ac9229c67059486f389
/// @audit 128
98:           if (tick & 0x4 != 0) ratio = (ratio * 0xfff2e50f5f656ac9229c67059486f389) >> 128;

/// @audit 0x8
/// @audit 0xffe5caca7e10e81259b3cddc7a064941
/// @audit 128
99:           if (tick & 0x8 != 0) ratio = (ratio * 0xffe5caca7e10e81259b3cddc7a064941) >> 128;

/// @audit 0x10
/// @audit 0xffcb9843d60f67b19e8887e0bd251eb7
/// @audit 128
100:          if (tick & 0x10 != 0) ratio = (ratio * 0xffcb9843d60f67b19e8887e0bd251eb7) >> 128;

/// @audit 0x20
/// @audit 0xff973b41fa98cd2e57b660be99eb2c4a
/// @audit 128
101:          if (tick & 0x20 != 0) ratio = (ratio * 0xff973b41fa98cd2e57b660be99eb2c4a) >> 128;

/// @audit 0x40
/// @audit 0xff2ea16466c9838804e327cb417cafcb
/// @audit 128
102:          if (tick & 0x40 != 0) ratio = (ratio * 0xff2ea16466c9838804e327cb417cafcb) >> 128;

/// @audit 0x80
/// @audit 0xfe5dee046a99d51e2cc356c2f617dbe0
/// @audit 128
103:          if (tick & 0x80 != 0) ratio = (ratio * 0xfe5dee046a99d51e2cc356c2f617dbe0) >> 128;

/// @audit 0x100
/// @audit 0xfcbe86c7900aecf64236ab31f1f9dcb5
/// @audit 128
104:          if (tick & 0x100 != 0) ratio = (ratio * 0xfcbe86c7900aecf64236ab31f1f9dcb5) >> 128;

/// @audit 0x200
/// @audit 0xf987a7253ac4d9194200696907cf2e37
/// @audit 128
105:          if (tick & 0x200 != 0) ratio = (ratio * 0xf987a7253ac4d9194200696907cf2e37) >> 128;

/// @audit 0x400
/// @audit 0xf3392b0822b88206f8abe8a3b44dd9be
/// @audit 128
106:          if (tick & 0x400 != 0) ratio = (ratio * 0xf3392b0822b88206f8abe8a3b44dd9be) >> 128;

/// @audit 0x800
/// @audit 0xe7159475a2c578ef4f1d17b2b235d480
/// @audit 128
107:          if (tick & 0x800 != 0) ratio = (ratio * 0xe7159475a2c578ef4f1d17b2b235d480) >> 128;

/// @audit 0x1000
/// @audit 0xd097f3bdfd254ee83bdd3f248e7e785e
/// @audit 128
108:          if (tick & 0x1000 != 0) ratio = (ratio * 0xd097f3bdfd254ee83bdd3f248e7e785e) >> 128;

/// @audit 0x2000
/// @audit 0xa9f746462d8f7dd10e744d913d033333
/// @audit 128
109:          if (tick & 0x2000 != 0) ratio = (ratio * 0xa9f746462d8f7dd10e744d913d033333) >> 128;

/// @audit 0x4000
/// @audit 0x70d869a156ddd32a39e257bc3f50aa9b
/// @audit 128
110:          if (tick & 0x4000 != 0) ratio = (ratio * 0x70d869a156ddd32a39e257bc3f50aa9b) >> 128;

/// @audit 0x8000
/// @audit 0x31be135f97da6e09a19dc367e3b6da40
/// @audit 128
111:          if (tick & 0x8000 != 0) ratio = (ratio * 0x31be135f97da6e09a19dc367e3b6da40) >> 128;

/// @audit 0x10000
/// @audit 0x9aa508b5b7e5a9780b0cc4e25d61a56
/// @audit 128
112:          if (tick & 0x10000 != 0) ratio = (ratio * 0x9aa508b5b7e5a9780b0cc4e25d61a56) >> 128;

/// @audit 0x20000
/// @audit 0x5d6af8dedbcb3a6ccb7ce618d14225
/// @audit 128
113:          if (tick & 0x20000 != 0) ratio = (ratio * 0x5d6af8dedbcb3a6ccb7ce618d14225) >> 128;

/// @audit 0x40000
/// @audit 0x2216e584f630389b2052b8db590e
/// @audit 128
114:          if (tick & 0x40000 != 0) ratio = (ratio * 0x2216e584f630389b2052b8db590e) >> 128;

/// @audit 128
116:          uint256 result = (ratio * PRBMathUD60x18.SCALE) >> 128;

/// @audit 4
127:                      b + (b.mul(b) + Math.mulDiv(4 * _reserveB.mul(_reserveA), _sqrtUpperTickPrice - _sqrtLowerTickPrice, _sqrtUpperTickPrice, false)).sqrt(),

```
https://github.com/code-423n4/2022-12-Stealth-Project/blob/fc8589d7d8c1d8488fd97ccc46e1ff11c8426ac2/maverick-v1/contracts/libraries/BinMath.sol#L16

```solidity
File: maverick-v1/contracts/models/Factory.sol

/// @audit 1e18
60:           require(_fee > 0 && _fee < 1e18, "Factory:FEE_OUT_OF_BOUNDS");

/// @audit 3600
62:           require(_lookback >= 3600 && _lookback <= uint16(type(int16).max), "Factory:LOOKBACK_OUT_OF_BOUNDS");

```
https://github.com/code-423n4/2022-12-Stealth-Project/blob/fc8589d7d8c1d8488fd97ccc46e1ff11c8426ac2/maverick-v1/contracts/models/Factory.sol#L60

```solidity
File: maverick-v1/contracts/models/Pool.sol

/// @audit 0x1111111111111111111111111111111111111111111111111111111111111111
462:                      0x1111111111111111111111111111111111111111111111111111111111111111 * (KIND_RIGHT | KIND_BOTH)

/// @audit 0x1111111111111111111111111111111111111111111111111111111111111111
479:                      0x1111111111111111111111111111111111111111111111111111111111111111 * (KIND_LEFT | KIND_BOTH)

```
https://github.com/code-423n4/2022-12-Stealth-Project/blob/fc8589d7d8c1d8488fd97ccc46e1ff11c8426ac2/maverick-v1/contracts/models/Pool.sol#L462

```solidity
File: router-v1/contracts/libraries/Multicall.sol

/// @audit 68
18:                   if (result.length < 68) revert();

/// @audit 0x04
20:                       result := add(result, 0x04)

```
https://github.com/code-423n4/2022-12-Stealth-Project/blob/fc8589d7d8c1d8488fd97ccc46e1ff11c8426ac2/router-v1/contracts/libraries/Multicall.sol#L18

### [N&#x2011;03]  Numeric values having to do with time should use time units for readability
There are [units](https://docs.soliditylang.org/en/latest/units-and-global-variables.html#time-units) for seconds, minutes, hours, days, and weeks, and since they're defined, they should be used

*There is 1 instance of this issue:*

```solidity
File: maverick-v1/contracts/models/Factory.sol

/// @audit 3600
62:           require(_lookback >= 3600 && _lookback <= uint16(type(int16).max), "Factory:LOOKBACK_OUT_OF_BOUNDS");

```
https://github.com/code-423n4/2022-12-Stealth-Project/blob/fc8589d7d8c1d8488fd97ccc46e1ff11c8426ac2/maverick-v1/contracts/models/Factory.sol#L62

### [N&#x2011;04]  Use bit shifts in an imutable variable rather than long bit masks of a single bit, for readability

*There are 4 instances of this issue:*

```solidity
File: maverick-v1/contracts/libraries/BinMath.sol

16:               if (x >= 0x100000000000000000000000000000000) {

20:               if (x >= 0x10000000000000000) {

24:               if (x >= 0x100000000) {

96:           uint256 ratio = tick & 0x1 != 0 ? 0xfffcb933bd6fad9d3af5f0b9f25db4d6 : 0x100000000000000000000000000000000;

```
https://github.com/code-423n4/2022-12-Stealth-Project/blob/fc8589d7d8c1d8488fd97ccc46e1ff11c8426ac2/maverick-v1/contracts/libraries/BinMath.sol#L16

### [N&#x2011;05]  Missing event and or timelock for critical parameter change
Events help non-contract tools to track changes, and events prevent users from being surprised by changes

*There is 1 instance of this issue:*

```solidity
File: maverick-v1/contracts/models/PositionMetadata.sol

17        function setBaseURI(string memory _baseURI) external onlyOwner {
18            baseURI = _baseURI;
19:       }

```
https://github.com/code-423n4/2022-12-Stealth-Project/blob/fc8589d7d8c1d8488fd97ccc46e1ff11c8426ac2/maverick-v1/contracts/models/PositionMetadata.sol#L17-L19

### [N&#x2011;06]  Events that mark critical parameter changes should contain both the old and the new value
This should especially be done if the new value is not required to be different from the old value

*There are 3 instances of this issue:*

```solidity
File: maverick-v1/contracts/models/Factory.sol

/// @audit setProtocolFeeRatio()
37:           emit SetFactoryProtocolFeeRatio(_protocolFeeRatio);

/// @audit setOwner()
43:           emit SetFactoryOwner(_owner);

```
https://github.com/code-423n4/2022-12-Stealth-Project/blob/fc8589d7d8c1d8488fd97ccc46e1ff11c8426ac2/maverick-v1/contracts/models/Factory.sol#L37

```solidity
File: maverick-v1/contracts/models/Position.sol

/// @audit setMetadata()
25:           emit SetMetadata(metadata);

```
https://github.com/code-423n4/2022-12-Stealth-Project/blob/fc8589d7d8c1d8488fd97ccc46e1ff11c8426ac2/maverick-v1/contracts/models/Position.sol#L25

### [N&#x2011;07]  Use a more recent version of solidity
Use a solidity version of at least 0.8.12 to get `string.concat()` to be used instead of `abi.encodePacked(<str>,<str>)`

*There is 1 instance of this issue:*

```solidity
File: maverick-v1/contracts/models/PositionMetadata.sol

2:    pragma solidity ^0.8.4;

```
https://github.com/code-423n4/2022-12-Stealth-Project/blob/fc8589d7d8c1d8488fd97ccc46e1ff11c8426ac2/maverick-v1/contracts/models/PositionMetadata.sol#L2

### [N&#x2011;08]  Use a more recent version of solidity
Use a solidity version of at least 0.8.13 to get the ability to use `using for` with a list of free functions

*There are 8 instances of this issue:*

```solidity
File: maverick-v1/contracts/libraries/BinMath.sol

2:    pragma solidity ^0.8.0;

```
https://github.com/code-423n4/2022-12-Stealth-Project/blob/fc8589d7d8c1d8488fd97ccc46e1ff11c8426ac2/maverick-v1/contracts/libraries/BinMath.sol#L2

```solidity
File: maverick-v1/contracts/libraries/Bin.sol

2:    pragma solidity ^0.8.0;

```
https://github.com/code-423n4/2022-12-Stealth-Project/blob/fc8589d7d8c1d8488fd97ccc46e1ff11c8426ac2/maverick-v1/contracts/libraries/Bin.sol#L2

```solidity
File: maverick-v1/contracts/libraries/SafeERC20Min.sol

3:    pragma solidity ^0.8.0;

```
https://github.com/code-423n4/2022-12-Stealth-Project/blob/fc8589d7d8c1d8488fd97ccc46e1ff11c8426ac2/maverick-v1/contracts/libraries/SafeERC20Min.sol#L3

```solidity
File: maverick-v1/contracts/libraries/Twa.sol

2:    pragma solidity ^0.8.0;

```
https://github.com/code-423n4/2022-12-Stealth-Project/blob/fc8589d7d8c1d8488fd97ccc46e1ff11c8426ac2/maverick-v1/contracts/libraries/Twa.sol#L2

```solidity
File: maverick-v1/contracts/models/PoolInspector.sol

2:    pragma solidity ^0.8.0;

```
https://github.com/code-423n4/2022-12-Stealth-Project/blob/fc8589d7d8c1d8488fd97ccc46e1ff11c8426ac2/maverick-v1/contracts/models/PoolInspector.sol#L2

```solidity
File: maverick-v1/contracts/models/Pool.sol

2:    pragma solidity ^0.8.0;

```
https://github.com/code-423n4/2022-12-Stealth-Project/blob/fc8589d7d8c1d8488fd97ccc46e1ff11c8426ac2/maverick-v1/contracts/models/Pool.sol#L2

```solidity
File: maverick-v1/contracts/models/Position.sol

2:    pragma solidity ^0.8.4;

```
https://github.com/code-423n4/2022-12-Stealth-Project/blob/fc8589d7d8c1d8488fd97ccc46e1ff11c8426ac2/maverick-v1/contracts/models/Position.sol#L2

```solidity
File: router-v1/contracts/libraries/Path.sol

2:    pragma solidity ^0.8.0;

```
https://github.com/code-423n4/2022-12-Stealth-Project/blob/fc8589d7d8c1d8488fd97ccc46e1ff11c8426ac2/router-v1/contracts/libraries/Path.sol#L2

### [N&#x2011;09]  Use a more recent version of solidity
Use a solidity version of at least 0.8.4 to get `bytes.concat()` instead of `abi.encodePacked(<bytes>,<bytes>)`
Use a solidity version of at least 0.8.12 to get `string.concat()` instead of `abi.encodePacked(<str>,<str>)`

*There is 1 instance of this issue:*

```solidity
File: router-v1/contracts/Router.sol

2:    pragma solidity ^0.8.0;

```
https://github.com/code-423n4/2022-12-Stealth-Project/blob/fc8589d7d8c1d8488fd97ccc46e1ff11c8426ac2/router-v1/contracts/Router.sol#L2

### [N&#x2011;10]  Inconsistent spacing in comments
Some lines use `// x` and some use `//x`. The instances below point out the usages that don't follow the majority, within each file

*There are 28 instances of this issue:*

```solidity
File: maverick-v1/contracts/interfaces/IFactory.sol

29:       //protocol

```
https://github.com/code-423n4/2022-12-Stealth-Project/blob/fc8589d7d8c1d8488fd97ccc46e1ff11c8426ac2/maverick-v1/contracts/interfaces/IFactory.sol#L29

```solidity
File: maverick-v1/contracts/interfaces/IPoolAdmin.sol

7:        //or B protocol fee (2 or 3)

```
https://github.com/code-423n4/2022-12-Stealth-Project/blob/fc8589d7d8c1d8488fd97ccc46e1ff11c8426ac2/maverick-v1/contracts/interfaces/IPoolAdmin.sol#L7

```solidity
File: maverick-v1/contracts/interfaces/IPool.sol

78:       //to the current bin or an absolute position

100:      //defined in Pool.sol

103:      //protocol

147:      //position of the liquidity

149:      //caller can transfer tokens

153:      //to another

163:      //and amounts

167:      //mergeId is the currrent active bin.

170:      //zero to recurse until the active bin is found.

176:      //is false or the output if exactOutput is true

179:      //exact output amount (true)

181:      //indicates no limit.  Limit is only engaged for exactOutput=false.  If the

182:      //limit is reached only part of the input amount will be swapped and the

183:      //callback will only require that amount of the swap to be paid.

185:      //caller can transfer tokens

```
https://github.com/code-423n4/2022-12-Stealth-Project/blob/fc8589d7d8c1d8488fd97ccc46e1ff11c8426ac2/maverick-v1/contracts/interfaces/IPool.sol#L78

```solidity
File: maverick-v1/contracts/models/Pool.sol

356:      //**********************  Internal  **********************/

```
https://github.com/code-423n4/2022-12-Stealth-Project/blob/fc8589d7d8c1d8488fd97ccc46e1ff11c8426ac2/maverick-v1/contracts/models/Pool.sol#L356

```solidity
File: router-v1/contracts/interfaces/IRouter.sol

33:       //another token

35:       //`ExactInputSingleParams` in calldata

48:       //another along the specified path

50:       //as `ExactInputParams` in calldata

65:       //another token

67:       //`ExactOutputSingleParams` in calldata

80:       //another along the specified path (reversed)

82:       //as `ExactOutputParams` in calldata

```
https://github.com/code-423n4/2022-12-Stealth-Project/blob/fc8589d7d8c1d8488fd97ccc46e1ff11c8426ac2/router-v1/contracts/interfaces/IRouter.sol#L33

```solidity
File: router-v1/contracts/Router.sol

21:       //computed amount in for an exact output swap / can never actually be this

22:       //value

```
https://github.com/code-423n4/2022-12-Stealth-Project/blob/fc8589d7d8c1d8488fd97ccc46e1ff11c8426ac2/router-v1/contracts/Router.sol#L21

### [N&#x2011;11]  Lines are too long
Usually lines in source code are limited to [80](https://softwareengineering.stackexchange.com/questions/148677/why-is-80-characters-the-standard-limit-for-code-width) characters. Today's screens are much larger so it's reasonable to stretch this in some cases. Since the files will most likely reside in GitHub, and GitHub starts using a scroll bar in all cases when the length is over [164](https://github.com/aizatto/character-length) characters, the lines below should be split when they reach that length

*There are 18 instances of this issue:*

```solidity
File: maverick-v1/contracts/interfaces/IFactory.sol

9:        event PoolCreated(address poolAddress, uint256 fee, uint256 tickSpacing, int32 activeTick, uint32 lookback, uint16 protocolFeeRatio, IERC20 tokenA, IERC20 tokenB);

```
https://github.com/code-423n4/2022-12-Stealth-Project/blob/fc8589d7d8c1d8488fd97ccc46e1ff11c8426ac2/maverick-v1/contracts/interfaces/IFactory.sol#L9

```solidity
File: maverick-v1/contracts/interfaces/IPool.sol

150:      function addLiquidity(uint256 tokenId, AddLiquidityParams[] calldata params, bytes calldata data) external returns (uint256 tokenAAmount, uint256 tokenBAmount, BinDelta[] memory binDeltas);

164:      function removeLiquidity(address recipient, uint256 tokenId, RemoveLiquidityParams[] calldata params) external returns (uint256 tokenAOut, uint256 tokenBOut, BinDelta[] memory binDeltas);

186:      function swap(address recipient, uint256 amount, bool tokenAIn, bool exactOutput, uint256 sqrtPriceLimit, bytes calldata data) external returns (uint256 amountIn, uint256 amountOut);

```
https://github.com/code-423n4/2022-12-Stealth-Project/blob/fc8589d7d8c1d8488fd97ccc46e1ff11c8426ac2/maverick-v1/contracts/interfaces/IPool.sol#L150

```solidity
File: maverick-v1/contracts/libraries/BinMap.sol

41:       function getKindsAtTickRange(mapping(int32 => uint256) storage binMap, int32 startTick, int32 endTick, uint256 kindMask) internal view returns (Active[] memory activeList, uint256 counter) {

```
https://github.com/code-423n4/2022-12-Stealth-Project/blob/fc8589d7d8c1d8488fd97ccc46e1ff11c8426ac2/maverick-v1/contracts/libraries/BinMap.sol#L41

```solidity
File: maverick-v1/contracts/libraries/BinMath.sol

134:      function getTickSqrtPriceAndL(uint256 _reserveA, uint256 _reserveB, uint256 _sqrtLowerTickPrice, uint256 _sqrtUpperTickPrice) internal pure returns (uint256 sqrtPrice, uint256 liquidity) {

```
https://github.com/code-423n4/2022-12-Stealth-Project/blob/fc8589d7d8c1d8488fd97ccc46e1ff11c8426ac2/maverick-v1/contracts/libraries/BinMath.sol#L134

```solidity
File: maverick-v1/contracts/libraries/Bin.sol

52:                   : Math.min(Math.mulDiv(deltaAOptimal, self.state.totalSupply, self.state.reserveA, false), Math.mulDiv(deltaBOptimal, self.state.totalSupply, self.state.reserveB, false));

```
https://github.com/code-423n4/2022-12-Stealth-Project/blob/fc8589d7d8c1d8488fd97ccc46e1ff11c8426ac2/maverick-v1/contracts/libraries/Bin.sol#L52

```solidity
File: maverick-v1/contracts/models/Factory.sol

58:       function create(uint256 _fee, uint256 _tickSpacing, uint16 _lookback, int32 _activeTick, IERC20 _tokenA, IERC20 _tokenB) external override returns (IPool pool) {

```
https://github.com/code-423n4/2022-12-Stealth-Project/blob/fc8589d7d8c1d8488fd97ccc46e1ff11c8426ac2/maverick-v1/contracts/models/Factory.sol#L58

```solidity
File: maverick-v1/contracts/models/PoolInspector.sol

33:                   bins[activeCounter] = BinInfo({id: i + 1, kind: bin.kind, lowerTick: bin.lowerTick, reserveA: bin.reserveA, reserveB: bin.reserveB, mergeId: bin.mergeId});

107:          (sqrtPrice, liquidity) = BinMath.getTickSqrtPriceAndL(reserveA, reserveB, BinMath.tickSqrtPrice(tickSpacing, activeTick), BinMath.tickSqrtPrice(tickSpacing, activeTick + 1));

```
https://github.com/code-423n4/2022-12-Stealth-Project/blob/fc8589d7d8c1d8488fd97ccc46e1ff11c8426ac2/maverick-v1/contracts/models/PoolInspector.sol#L33

```solidity
File: maverick-v1/contracts/models/Pool.sol

238:              binDeltas[i] = BinDelta({binId: param.binId, deltaA: deltaA, deltaB: deltaB, kind: bin.state.kind, isActive: isActive, lowerTick: bin.state.lowerTick, deltaLpBalance: deltaLpBalance});

260:      function swap(address recipient, uint256 amount, bool tokenAIn, bool exactOutput, uint256 sqrtPriceLimit, bytes calldata data) external returns (uint256 amountIn, uint256 amountOut) {

270:              delta.excess = (!exactOutput) ? Math.fromScale(amount, tokenAIn ? tokenAScale : tokenBScale) : Math.fromScale(amount, !tokenAIn ? tokenAScale : tokenBScale);

294:              twa.updateValue(currentState.activeTick * PRBMathSD59x18.SCALE + int256(Math.clip(delta.endSqrtPrice, delta.sqrtLowerTickPrice).div(delta.sqrtUpperTickPrice - delta.sqrtLowerTickPrice)));

516:      function _currentTickLiquidity(int32 tick) internal view returns (uint256 sqrtLowerTickPrice, uint256 sqrtUpperTickPrice, uint256 sqrtPrice, uint128[] memory binIds, SwapData memory output) {

597:      function _computeSwapExactOut(uint256 sqrtPrice, uint256 liquidity, uint256 reserveA, uint256 reserveB, uint256 amountOut, bool tokenAIn) internal view returns (Delta.Instance memory delta) {

602:          binAmountIn = Math.mulDiv(delta.deltaOutErc, tokenAIn ? sqrtPrice : sqrtPrice.inv(), (tokenAIn ? sqrtPrice.inv() : sqrtPrice) - delta.deltaOutErc.div(liquidity), true);

```
https://github.com/code-423n4/2022-12-Stealth-Project/blob/fc8589d7d8c1d8488fd97ccc46e1ff11c8426ac2/maverick-v1/contracts/models/Pool.sol#L238

```solidity
File: router-v1/contracts/Router.sol

272:              pool = IFactory(factory).create(poolParams.fee, poolParams.tickSpacing, poolParams.lookback, poolParams.activeTick, poolParams.tokenA, poolParams.tokenB);

```
https://github.com/code-423n4/2022-12-Stealth-Project/blob/fc8589d7d8c1d8488fd97ccc46e1ff11c8426ac2/router-v1/contracts/Router.sol#L272

### [N&#x2011;12]  Variable names that consist of all capital letters should be reserved for `constant`/`immutable` variables
If the variable needs to be different based on which class it comes from, a `view`/`pure` _function_ should be used instead (e.g. like [this](https://github.com/OpenZeppelin/openzeppelin-contracts/blob/76eee35971c2541585e05cbf258510dda7b2fbc6/contracts/token/ERC20/extensions/draft-IERC20Permit.sol#L59)).

*There is 1 instance of this issue:*

```solidity
File: router-v1/contracts/Router.sol

48:       constructor(IFactory _factory, IPosition _position, IWETH9 _WETH9) {

```
https://github.com/code-423n4/2022-12-Stealth-Project/blob/fc8589d7d8c1d8488fd97ccc46e1ff11c8426ac2/router-v1/contracts/Router.sol#L48

### [N&#x2011;13]  Non-library/interface files should use fixed compiler versions, not floating ones

*There are 6 instances of this issue:*

```solidity
File: maverick-v1/contracts/models/Factory.sol

2:    pragma solidity ^0.8.0;

```
https://github.com/code-423n4/2022-12-Stealth-Project/blob/fc8589d7d8c1d8488fd97ccc46e1ff11c8426ac2/maverick-v1/contracts/models/Factory.sol#L2

```solidity
File: maverick-v1/contracts/models/PoolInspector.sol

2:    pragma solidity ^0.8.0;

```
https://github.com/code-423n4/2022-12-Stealth-Project/blob/fc8589d7d8c1d8488fd97ccc46e1ff11c8426ac2/maverick-v1/contracts/models/PoolInspector.sol#L2

```solidity
File: maverick-v1/contracts/models/Pool.sol

2:    pragma solidity ^0.8.0;

```
https://github.com/code-423n4/2022-12-Stealth-Project/blob/fc8589d7d8c1d8488fd97ccc46e1ff11c8426ac2/maverick-v1/contracts/models/Pool.sol#L2

```solidity
File: maverick-v1/contracts/models/PositionMetadata.sol

2:    pragma solidity ^0.8.4;

```
https://github.com/code-423n4/2022-12-Stealth-Project/blob/fc8589d7d8c1d8488fd97ccc46e1ff11c8426ac2/maverick-v1/contracts/models/PositionMetadata.sol#L2

```solidity
File: maverick-v1/contracts/models/Position.sol

2:    pragma solidity ^0.8.4;

```
https://github.com/code-423n4/2022-12-Stealth-Project/blob/fc8589d7d8c1d8488fd97ccc46e1ff11c8426ac2/maverick-v1/contracts/models/Position.sol#L2

```solidity
File: router-v1/contracts/Router.sol

2:    pragma solidity ^0.8.0;

```
https://github.com/code-423n4/2022-12-Stealth-Project/blob/fc8589d7d8c1d8488fd97ccc46e1ff11c8426ac2/router-v1/contracts/Router.sol#L2

### [N&#x2011;14]  File is missing NatSpec

*There are 18 instances of this issue:*

```solidity
File: maverick-v1/contracts/interfaces/IAddLiquidityCallback.sol


```
https://github.com/code-423n4/2022-12-Stealth-Project/blob/fc8589d7d8c1d8488fd97ccc46e1ff11c8426ac2/maverick-v1/contracts/interfaces/IAddLiquidityCallback.sol

```solidity
File: maverick-v1/contracts/interfaces/IPositionMetadata.sol


```
https://github.com/code-423n4/2022-12-Stealth-Project/blob/fc8589d7d8c1d8488fd97ccc46e1ff11c8426ac2/maverick-v1/contracts/interfaces/IPositionMetadata.sol

```solidity
File: maverick-v1/contracts/interfaces/ISwapCallback.sol


```
https://github.com/code-423n4/2022-12-Stealth-Project/blob/fc8589d7d8c1d8488fd97ccc46e1ff11c8426ac2/maverick-v1/contracts/interfaces/ISwapCallback.sol

```solidity
File: maverick-v1/contracts/libraries/BinMap.sol


```
https://github.com/code-423n4/2022-12-Stealth-Project/blob/fc8589d7d8c1d8488fd97ccc46e1ff11c8426ac2/maverick-v1/contracts/libraries/BinMap.sol

```solidity
File: maverick-v1/contracts/libraries/BinMath.sol


```
https://github.com/code-423n4/2022-12-Stealth-Project/blob/fc8589d7d8c1d8488fd97ccc46e1ff11c8426ac2/maverick-v1/contracts/libraries/BinMath.sol

```solidity
File: maverick-v1/contracts/libraries/Bin.sol


```
https://github.com/code-423n4/2022-12-Stealth-Project/blob/fc8589d7d8c1d8488fd97ccc46e1ff11c8426ac2/maverick-v1/contracts/libraries/Bin.sol

```solidity
File: maverick-v1/contracts/libraries/Cast.sol


```
https://github.com/code-423n4/2022-12-Stealth-Project/blob/fc8589d7d8c1d8488fd97ccc46e1ff11c8426ac2/maverick-v1/contracts/libraries/Cast.sol

```solidity
File: maverick-v1/contracts/libraries/Constants.sol


```
https://github.com/code-423n4/2022-12-Stealth-Project/blob/fc8589d7d8c1d8488fd97ccc46e1ff11c8426ac2/maverick-v1/contracts/libraries/Constants.sol

```solidity
File: maverick-v1/contracts/libraries/Delta.sol


```
https://github.com/code-423n4/2022-12-Stealth-Project/blob/fc8589d7d8c1d8488fd97ccc46e1ff11c8426ac2/maverick-v1/contracts/libraries/Delta.sol

```solidity
File: maverick-v1/contracts/libraries/Deployer.sol


```
https://github.com/code-423n4/2022-12-Stealth-Project/blob/fc8589d7d8c1d8488fd97ccc46e1ff11c8426ac2/maverick-v1/contracts/libraries/Deployer.sol

```solidity
File: maverick-v1/contracts/libraries/Math.sol


```
https://github.com/code-423n4/2022-12-Stealth-Project/blob/fc8589d7d8c1d8488fd97ccc46e1ff11c8426ac2/maverick-v1/contracts/libraries/Math.sol

```solidity
File: maverick-v1/contracts/libraries/SafeERC20Min.sol


```
https://github.com/code-423n4/2022-12-Stealth-Project/blob/fc8589d7d8c1d8488fd97ccc46e1ff11c8426ac2/maverick-v1/contracts/libraries/SafeERC20Min.sol

```solidity
File: maverick-v1/contracts/libraries/Twa.sol


```
https://github.com/code-423n4/2022-12-Stealth-Project/blob/fc8589d7d8c1d8488fd97ccc46e1ff11c8426ac2/maverick-v1/contracts/libraries/Twa.sol

```solidity
File: maverick-v1/contracts/models/PoolInspector.sol


```
https://github.com/code-423n4/2022-12-Stealth-Project/blob/fc8589d7d8c1d8488fd97ccc46e1ff11c8426ac2/maverick-v1/contracts/models/PoolInspector.sol

```solidity
File: maverick-v1/contracts/models/Pool.sol


```
https://github.com/code-423n4/2022-12-Stealth-Project/blob/fc8589d7d8c1d8488fd97ccc46e1ff11c8426ac2/maverick-v1/contracts/models/Pool.sol

```solidity
File: maverick-v1/contracts/models/PositionMetadata.sol


```
https://github.com/code-423n4/2022-12-Stealth-Project/blob/fc8589d7d8c1d8488fd97ccc46e1ff11c8426ac2/maverick-v1/contracts/models/PositionMetadata.sol

```solidity
File: maverick-v1/contracts/models/Position.sol


```
https://github.com/code-423n4/2022-12-Stealth-Project/blob/fc8589d7d8c1d8488fd97ccc46e1ff11c8426ac2/maverick-v1/contracts/models/Position.sol

```solidity
File: router-v1/contracts/libraries/Deadline.sol


```
https://github.com/code-423n4/2022-12-Stealth-Project/blob/fc8589d7d8c1d8488fd97ccc46e1ff11c8426ac2/router-v1/contracts/libraries/Deadline.sol

### [N&#x2011;15]  NatSpec is incomplete

*There are 9 instances of this issue:*

```solidity
File: maverick-v1/contracts/interfaces/IFactory.sol

/// @audit Missing: '@return'
18        /// @param _tokenA ERC20 token
19        /// @param _tokenB ERC20 token
20:       function create(uint256 _fee, uint256 _tickSpacing, uint16 _lookback, int32 _activeTick, IERC20 _tokenA, IERC20 _tokenB) external returns (IPool);

```
https://github.com/code-423n4/2022-12-Stealth-Project/blob/fc8589d7d8c1d8488fd97ccc46e1ff11c8426ac2/maverick-v1/contracts/interfaces/IFactory.sol#L18-L20

```solidity
File: maverick-v1/contracts/interfaces/IPool.sol

/// @audit Missing: '@return'
148       /// @param data callback function that addLiquidity will call so that the
149       //caller can transfer tokens
150:      function addLiquidity(uint256 tokenId, AddLiquidityParams[] calldata params, bytes calldata data) external returns (uint256 tokenAAmount, uint256 tokenBAmount, BinDelta[] memory binDeltas);

/// @audit Missing: '@return'
162       /// @param params array of RemoveLiquidityParams that specify the bins,
163       //and amounts
164:      function removeLiquidity(address recipient, uint256 tokenId, RemoveLiquidityParams[] calldata params) external returns (uint256 tokenAOut, uint256 tokenBOut, BinDelta[] memory binDeltas);

/// @audit Missing: '@return'
184       /// @param data callback function that swap will call so that the
185       //caller can transfer tokens
186:      function swap(address recipient, uint256 amount, bool tokenAIn, bool exactOutput, uint256 sqrtPriceLimit, bytes calldata data) external returns (uint256 amountIn, uint256 amountOut);

```
https://github.com/code-423n4/2022-12-Stealth-Project/blob/fc8589d7d8c1d8488fd97ccc46e1ff11c8426ac2/maverick-v1/contracts/interfaces/IPool.sol#L148-L150

```solidity
File: maverick-v1/contracts/models/Factory.sol

/// @audit Missing: '@return'
56        /// @param _tokenA ERC20 token
57        /// @param _tokenB ERC20 token
58:       function create(uint256 _fee, uint256 _tickSpacing, uint16 _lookback, int32 _activeTick, IERC20 _tokenA, IERC20 _tokenB) external override returns (IPool pool) {

```
https://github.com/code-423n4/2022-12-Stealth-Project/blob/fc8589d7d8c1d8488fd97ccc46e1ff11c8426ac2/maverick-v1/contracts/models/Factory.sol#L56-L58

```solidity
File: router-v1/contracts/interfaces/IRouter.sol

/// @audit Missing: '@return'
118       /// @param minTokenBAmount minimum amount of token B to add, revert if not met
119       /// @param deadline epoch timestamp in seconds
120       function getOrCreatePoolAndAddLiquidity(
121           PoolParams calldata poolParams,
122           uint256 tokenId,
123           IPool.AddLiquidityParams[] calldata addParams,
124           uint256 minTokenAAmount,
125           uint256 minTokenBAmount,
126           uint256 deadline
127:      ) external payable returns (uint256 receivingTokenId, uint256 tokenAAmount, uint256 tokenBAmount, IPool.BinDelta[] memory binDeltas);

/// @audit Missing: '@return'
134       /// @param minTokenBAmount minimum amount of token B to add, revert if not met
135       /// @param deadline epoch timestamp in seconds
136       function addLiquidityToPool(
137           IPool pool,
138           uint256 tokenId,
139           IPool.AddLiquidityParams[] calldata params,
140           uint256 minTokenAAmount,
141           uint256 minTokenBAmount,
142           uint256 deadline
143:      ) external payable returns (uint256 receivingTokenId, uint256 tokenAAmount, uint256 tokenBAmount, IPool.BinDelta[] memory binDeltas);

/// @audit Missing: '@return'
152       /// @param maxActiveTick highest activeTick (inclusive) of pool that will permit transaction to pass
153       /// @param deadline epoch timestamp in seconds
154       function addLiquidityWTickLimits(
155           IPool pool,
156           uint256 tokenId,
157           IPool.AddLiquidityParams[] calldata params,
158           uint256 minTokenAAmount,
159           uint256 minTokenBAmount,
160           int32 minActiveTick,
161           int32 maxActiveTick,
162           uint256 deadline
163:      ) external payable returns (uint256 receivingTokenId, uint256 tokenAAmount, uint256 tokenBAmount, IPool.BinDelta[] memory binDeltas);

/// @audit Missing: '@return'
179       /// @param minTokenBAmount minimum amount of token B to receive, revert if not met
180       /// @param deadline epoch timestamp in seconds
181       function removeLiquidity(
182           IPool pool,
183           address recipient,
184           uint256 tokenId,
185           IPool.RemoveLiquidityParams[] calldata params,
186           uint256 minTokenAAmount,
187           uint256 minTokenBAmount,
188           uint256 deadline
189:      ) external returns (uint256 tokenAAmount, uint256 tokenBAmount, IPool.BinDelta[] memory binDeltas);

```
https://github.com/code-423n4/2022-12-Stealth-Project/blob/fc8589d7d8c1d8488fd97ccc46e1ff11c8426ac2/router-v1/contracts/interfaces/IRouter.sol#L118-L127

### [N&#x2011;16]  Not using the named return variables anywhere in the function is confusing
Consider changing the variable to be an unnamed one

*There are 17 instances of this issue:*

```solidity
File: maverick-v1/contracts/libraries/BinMath.sol

/// @audit _result
91:       function tickSqrtPrice(uint256 tickSpacing, int32 _tick) internal pure returns (uint256 _result) {

```
https://github.com/code-423n4/2022-12-Stealth-Project/blob/fc8589d7d8c1d8488fd97ccc46e1ff11c8426ac2/maverick-v1/contracts/libraries/BinMath.sol#L91

```solidity
File: maverick-v1/contracts/libraries/Delta.sol

/// @audit edge
38:       function sqrtEdgePrice(Instance memory self) internal pure returns (uint256 edge) {

```
https://github.com/code-423n4/2022-12-Stealth-Project/blob/fc8589d7d8c1d8488fd97ccc46e1ff11c8426ac2/maverick-v1/contracts/libraries/Delta.sol#L38

```solidity
File: maverick-v1/contracts/models/Factory.sol

/// @audit pool
46:       function lookup(uint256 _fee, uint256 _tickSpacing, uint16 _lookback, IERC20 _tokenA, IERC20 _tokenB) external view override returns (IPool pool) {

```
https://github.com/code-423n4/2022-12-Stealth-Project/blob/fc8589d7d8c1d8488fd97ccc46e1ff11c8426ac2/maverick-v1/contracts/models/Factory.sol#L46

```solidity
File: maverick-v1/contracts/models/Pool.sol

/// @audit bin
312:      function getBin(uint128 binId) external view override returns (BinState memory bin) {

/// @audit lpToken
316:      function balanceOf(uint256 tokenId, uint128 binId) external view override returns (uint256 lpToken) {

```
https://github.com/code-423n4/2022-12-Stealth-Project/blob/fc8589d7d8c1d8488fd97ccc46e1ff11c8426ac2/maverick-v1/contracts/models/Pool.sol#L312

```solidity
File: router-v1/contracts/Router.sol

/// @audit receivingTokenId
/// @audit tokenAAmount
/// @audit tokenBAmount
/// @audit binDeltas
238       function addLiquidityToPool(
239           IPool pool,
240           uint256 tokenId,
241           IPool.AddLiquidityParams[] calldata params,
242           uint256 minTokenAAmount,
243           uint256 minTokenBAmount,
244           uint256 deadline
245:      ) external payable checkDeadline(deadline) returns (uint256 receivingTokenId, uint256 tokenAAmount, uint256 tokenBAmount, IPool.BinDelta[] memory binDeltas) {

/// @audit receivingTokenId
/// @audit tokenAAmount
/// @audit tokenBAmount
/// @audit binDeltas
250       function addLiquidityWTickLimits(
251           IPool pool,
252           uint256 tokenId,
253           IPool.AddLiquidityParams[] calldata params,
254           uint256 minTokenAAmount,
255           uint256 minTokenBAmount,
256           int32 minActiveTick,
257           int32 maxActiveTick,
258           uint256 deadline
259:      ) external payable checkDeadline(deadline) returns (uint256 receivingTokenId, uint256 tokenAAmount, uint256 tokenBAmount, IPool.BinDelta[] memory binDeltas) {

/// @audit receivingTokenId
/// @audit tokenAAmount
/// @audit tokenBAmount
/// @audit binDeltas
277       function getOrCreatePoolAndAddLiquidity(
278           PoolParams calldata poolParams,
279           uint256 tokenId,
280           IPool.AddLiquidityParams[] calldata addParams,
281           uint256 minTokenAAmount,
282           uint256 minTokenBAmount,
283           uint256 deadline
284:      ) external payable checkDeadline(deadline) returns (uint256 receivingTokenId, uint256 tokenAAmount, uint256 tokenBAmount, IPool.BinDelta[] memory binDeltas) {

```
https://github.com/code-423n4/2022-12-Stealth-Project/blob/fc8589d7d8c1d8488fd97ccc46e1ff11c8426ac2/router-v1/contracts/Router.sol#L238-L245

### [N&#x2011;17]  Duplicated `require()`/`revert()` checks should be refactored to a modifier or function
The compiler will inline the function, which will avoid `JUMP` instructions usually associated with functions

*There are 3 instances of this issue:*

```solidity
File: maverick-v1/contracts/models/Factory.sol

35:           require(_protocolFeeRatio <= ONE_3_DECIMAL_SCALE, "Factory:PROTOCOL_FEE_CANNOT_EXCEED_ONE");

```
https://github.com/code-423n4/2022-12-Stealth-Project/blob/fc8589d7d8c1d8488fd97ccc46e1ff11c8426ac2/maverick-v1/contracts/models/Factory.sol#L35

```solidity
File: router-v1/contracts/Router.sol

165:          require(amountOut >= params.amountOutMinimum, "Too little received");

197:          require(amountIn <= params.amountInMaximum, "Too much requested");

```
https://github.com/code-423n4/2022-12-Stealth-Project/blob/fc8589d7d8c1d8488fd97ccc46e1ff11c8426ac2/router-v1/contracts/Router.sol#L165

### [N&#x2011;18]  Consider using `delete` rather than assigning zero to clear values
The `delete` keyword more closely matches the semantics of what is being done, and draws more attention to the changing of state, which may lead to a more thorough audit of its associated logic

*There are 6 instances of this issue:*

```solidity
File: maverick-v1/contracts/libraries/Delta.sol

43:           self.excess = 0;

```
https://github.com/code-423n4/2022-12-Stealth-Project/blob/fc8589d7d8c1d8488fd97ccc46e1ff11c8426ac2/maverick-v1/contracts/libraries/Delta.sol#L43

```solidity
File: maverick-v1/contracts/models/Pool.sol

359:          binPositions[tick][kind] = 0;

382:              moveData.firstBinId = 0;

384:              moveData.mergeBinCounter = 0;

385:              moveData.totalReserveA = 0;

386:              moveData.totalReserveB = 0;

```
https://github.com/code-423n4/2022-12-Stealth-Project/blob/fc8589d7d8c1d8488fd97ccc46e1ff11c8426ac2/maverick-v1/contracts/models/Pool.sol#L359

### [N&#x2011;19]  Contracts should have full test coverage
While 100% code coverage does not guarantee that there are no bugs, it often will catch easy-to-find bugs, and will ensure that there are fewer regressions when the code invariably has to be modified. Furthermore, in order to get full coverage, code authors will often have to re-organize their code so that it is more modular, so that each component can be tested separately, which reduces interdependencies between modules and layers, and makes for code that is easier to reason about and audit.

*There is 1 instance of this issue:*

```solidity
File: maverick-v1/contracts/interfaces/IAddLiquidityCallback.sol


```

### [N&#x2011;20]  Large or complicated code bases should implement fuzzing tests
Large code bases, or code with lots of inline-assembly, complicated math, or complicated interactions between multiple contracts, should implement [fuzzing tests](https://medium.com/coinmonks/smart-contract-fuzzing-d9b88e0b0a05). Fuzzers such as Echidna require the test writer to come up with invariants which should not be violated under any circumstances, and the fuzzer tests various inputs and function calls to ensure that the invariants always hold. Even code with 100% code coverage can still have bugs due to the order of the operations a user performs, and fuzzers, with properly and extensively-written invariants, can close this testing gap significantly.

*There is 1 instance of this issue:*

```solidity
File: maverick-v1/contracts/interfaces/IAddLiquidityCallback.sol


```


___

## Excluded findings
These findings are excluded from awards calculations because there are publicly-available automated tools that find them. The valid ones appear here for completeness

## Summary

### Non-critical Issues
| |Issue|Instances|
|-|:-|:-:|
| [N&#x2011;01] | `require()`/`revert()` statements should have descriptive reason strings | 7 | 
| [N&#x2011;02] | `public` functions not called by the contract should be declared `external` instead | 2 | 
| [N&#x2011;03] | Event is missing `indexed` fields | 12 | 

Total: 21 instances over 3 issues





## Non-critical Issues

### [N&#x2011;01]  `require()`/`revert()` statements should have descriptive reason strings

*There are 7 instances of this issue:*

```solidity
File: maverick-v1/contracts/models/Pool.sol

/// @audit (valid but excluded finding)
129:              require(param.kind < NUMBER_OF_KINDS);

/// @audit (valid but excluded finding)
333:          require(_protocolFeeRatio <= ONE_3_DECIMAL_SCALE);

/// @audit (valid but excluded finding)
342:          require(msg.sender == factory.owner());

```
https://github.com/code-423n4/2022-12-Stealth-Project/blob/fc8589d7d8c1d8488fd97ccc46e1ff11c8426ac2/maverick-v1/contracts/models/Pool.sol#L129

```solidity
File: router-v1/contracts/libraries/Multicall.sol

/// @audit (valid but excluded finding)
18:                   if (result.length < 68) revert();

```
https://github.com/code-423n4/2022-12-Stealth-Project/blob/fc8589d7d8c1d8488fd97ccc46e1ff11c8426ac2/router-v1/contracts/libraries/Multicall.sol#L18

```solidity
File: router-v1/contracts/Router.sol

/// @audit (valid but excluded finding)
106:          require(msg.sender == address(pool));

/// @audit (valid but excluded finding)
205:          require(factory.isFactoryPool(IPool(msg.sender)));

/// @audit (valid but excluded finding)
206:          require(msg.sender == address(data.pool));

```
https://github.com/code-423n4/2022-12-Stealth-Project/blob/fc8589d7d8c1d8488fd97ccc46e1ff11c8426ac2/router-v1/contracts/Router.sol#L106

### [N&#x2011;02]  `public` functions not called by the contract should be declared `external` instead
Contracts [are allowed](https://docs.soliditylang.org/en/latest/contracts.html#function-overriding) to override their parents' functions and change the visibility from `external` to `public`.

*There are 2 instances of this issue:*

```solidity
File: maverick-v1/contracts/models/PoolInspector.sol

/// @audit (valid but excluded finding)
45:       function getBinDepth(IPool pool, uint128 binId) public view returns (uint256 depth) {

```
https://github.com/code-423n4/2022-12-Stealth-Project/blob/fc8589d7d8c1d8488fd97ccc46e1ff11c8426ac2/maverick-v1/contracts/models/PoolInspector.sol#L45

```solidity
File: router-v1/contracts/Router.sol

/// @audit (valid but excluded finding)
70:       function sweepToken(IERC20 token, uint256 amountMinimum, address recipient) public payable {

```
https://github.com/code-423n4/2022-12-Stealth-Project/blob/fc8589d7d8c1d8488fd97ccc46e1ff11c8426ac2/router-v1/contracts/Router.sol#L70

### [N&#x2011;03]  Event is missing `indexed` fields
Index event fields make the field more quickly accessible [to off-chain tools](https://ethereum.stackexchange.com/questions/40396/can-somebody-please-explain-the-concept-of-event-indexing) that parse events. However, note that each index field costs extra gas during emission, so it's not necessarily best to index the maximum allowed per event (three fields). Each `event` should use three `indexed` fields if there are three or more fields, and gas usage is not particularly of concern for the events in question. If there are fewer than three fields, all of the fields should be indexed.

*There are 12 instances of this issue:*

```solidity
File: maverick-v1/contracts/interfaces/IFactory.sol

/// @audit (valid but excluded finding)
9:        event PoolCreated(address poolAddress, uint256 fee, uint256 tickSpacing, int32 activeTick, uint32 lookback, uint16 protocolFeeRatio, IERC20 tokenA, IERC20 tokenB);

/// @audit (valid but excluded finding)
10:       event SetFactoryProtocolFeeRatio(uint16 protocolFeeRatio);

/// @audit (valid but excluded finding)
11:       event SetFactoryOwner(address owner);

```
https://github.com/code-423n4/2022-12-Stealth-Project/blob/fc8589d7d8c1d8488fd97ccc46e1ff11c8426ac2/maverick-v1/contracts/interfaces/IFactory.sol#L9

```solidity
File: maverick-v1/contracts/interfaces/IPool.sol

/// @audit (valid but excluded finding)
8:        event Swap(address indexed sender, address indexed recipient, bool tokenAIn, bool exactOutput, uint256 amountIn, uint256 amountOut, int32 activeTick);

/// @audit (valid but excluded finding)
10:       event AddLiquidity(address indexed sender, uint256 indexed tokenId, BinDelta[] binDeltas);

/// @audit (valid but excluded finding)
12:       event MigrateBinsUpStack(address indexed sender, uint128[] binIds, uint32 maxRecursion);

/// @audit (valid but excluded finding)
14:       event TransferLiquidity(uint256 fromTokenId, uint256 toTokenId, RemoveLiquidityParams[] params);

/// @audit (valid but excluded finding)
18:       event BinMerged(uint128 indexed binId, uint128 reserveA, uint128 reserveB, uint128 mergeId);

/// @audit (valid but excluded finding)
20:       event BinMoved(uint128 indexed binId, int128 previousTick, int128 newTick);

/// @audit (valid but excluded finding)
22:       event ProtocolFeeCollected(uint256 protocolFee, bool isTokenA);

/// @audit (valid but excluded finding)
24:       event SetProtocolFeeRatio(uint256 protocolFee);

```
https://github.com/code-423n4/2022-12-Stealth-Project/blob/fc8589d7d8c1d8488fd97ccc46e1ff11c8426ac2/maverick-v1/contracts/interfaces/IPool.sol#L8

```solidity
File: maverick-v1/contracts/interfaces/IPosition.sol

/// @audit (valid but excluded finding)
8:        event SetMetadata(IPositionMetadata metadata);

```
https://github.com/code-423n4/2022-12-Stealth-Project/blob/fc8589d7d8c1d8488fd97ccc46e1ff11c8426ac2/maverick-v1/contracts/interfaces/IPosition.sol#L8
