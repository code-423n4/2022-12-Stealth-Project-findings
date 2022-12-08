- [Low](#low)
    - [**1. Mixing and Outdated compiler**](#1-mixing-and-outdated-compiler)
    - [**2. Unsafe safe library**](#2-unsafe-safe-library)
    - [**3. Lack of checks address0**](#3-lack-of-checks-address0)
    - [**4. Fix npm issues**](#4-fix-npm-issues)
        - [For maverick project we found:](#for-maverick-project-we-found)
        - [For router project we found:](#for-router-project-we-found)
    - [**5. Integer overflow by unsafe casting**](#5-integer-overflow-by-unsafe-casting)
- [Non critical](#non-critical)
    - [**6. Start with the token Id 0**](#6-start-with-the-token-id-0)
    - [**7. Use libraries instead of abstract contracts**](#7-use-libraries-instead-of-abstract-contracts)
    - [**8. Unify design**](#8-unify-design)

# Low

## **1. Mixing and Outdated compiler**

The pragma version used are:

```
pragma solidity >=0.5.0;
pragma solidity >=0.6.0;
pragma solidity >=0.7.5;
pragma solidity ^0.8.0;
```

*Note that mixing pragma is not recommended. Because different compiler versions have different meanings and behaviors, it also significantly raises maintenance costs. As a result, depending on the compiler version selected for any given file, deployed contracts may have security issues.*

The minimum required version must be [0.8.17](https://github.com/ethereum/solidity/releases/tag/v0.8.17); otherwise, contracts will be affected by the following **important bug fixes**:

[0.8.3](https://blog.soliditylang.org/2021/03/23/solidity-0.8.3-release-announcement/):

- Optimizer: Fix bug on incorrect caching of Keccak-256 hashes.

[0.8.4](https://blog.soliditylang.org/2021/04/21/solidity-0.8.4-release-announcement/):

- ABI Decoder V2: For two-dimensional arrays and specially crafted data in memory, the result of abi.decode can depend on data elsewhere in memory. Calldata decoding is not affected.

[0.8.9](https://blog.soliditylang.org/2021/09/29/solidity-0.8.9-release-announcement/):

- Immutables: Properly perform sign extension on signed immutables.
- User Defined Value Type: Fix storage layout of user defined value types for underlying types shorter than 32 bytes.

[0.8.13](https://blog.soliditylang.org/2022/03/16/solidity-0.8.13-release-announcement/):
- Code Generator: Correctly encode literals used in `abi.encodeCall` in place of fixed bytes arguments.

[0.8.14](https://blog.soliditylang.org/2022/05/18/solidity-0.8.14-release-announcement/):

- ABI Encoder: When ABI-encoding values from calldata that contain nested arrays, correctly validate the nested array length against `calldatasize()` in all cases.
- Override Checker: Allow changing data location for parameters only when overriding external functions.

[0.8.15](https://blog.soliditylang.org/2022/06/15/solidity-0.8.15-release-announcement/)

- Code Generation: Avoid writing dirty bytes to storage when copying `bytes` arrays.
- Yul Optimizer: Keep all memory side-effects of inline assembly blocks.

[0.8.16](https://blog.soliditylang.org/2022/08/08/solidity-0.8.16-release-announcement/)

- Code Generation: Fix data corruption that affected ABI-encoding of calldata values represented by tuples: structs at any nesting level; argument lists of external functions, events and errors; return value lists of external functions. The 32 leading bytes of the first dynamically-encoded value in the tuple would get zeroed when the last component contained a statically-encoded array.

[0.8.17](https://blog.soliditylang.org/2022/09/08/solidity-0.8.17-release-announcement/)

- Yul Optimizer: Prevent the incorrect removal of storage writes before calls to Yul functions that conditionally terminate the external EVM call.

Apart from these, there are several minor bug fixes and improvements.

## **2. Unsafe safe library**

The library used for safe ERC20 calls, is prone to a known attack.

Recently there was one of the biggest hacks (https://certik.medium.com/qubit-bridge-collapse-exploited-to-the-tune-of-80-million-a7ab9068e1a0) in crypto, 80m$ was lost.

> One of the root causes of the vulnerability was the fact that tokenAddress.safeTransferFrom() does not revert when the tokenAddress is the zero (null) address (0x0…000).

The audited contract doesn't use the last SafeERC20 version of openZeppelin; that library fixes the issue by checking that the `token` is a contract, instead of an EOA. It used a vulnerable version which doesn't check the kind of address. So the method `safeTransferFrom`, and `safeTransfer` doesn't check if `token` is a contract, only return value if it's different than 0.

The following methods are vulnerable to this:
- `safeTransferFrom`
- `safeTransfer`
- `safeApprove`
- `safeTransferETH`

Poc:
- Call this method with an EOA as a token, it will work.

*All of these calls are prone to lack of funds or wrong deposits.*

**Recommended Mitigation Steps**

- It's mandatory to check if the address is a contract.
- Use the last version of OpenZeppelin contracts, check that `token` is a contract and always check the returned value.

**Affected source code:**

- [TransferHelper.sol:6](https://github.com/code-423n4/2022-12-Stealth-Project/blob/fc8589d7d8c1d8488fd97ccc46e1ff11c8426ac2/router-v1/contracts/libraries/TransferHelper.sol#L6)

## **3. Lack of checks `address(0)`**

The following methods have a lack of checks if the received argument is an address, it's good practice in order to reduce human error to check that the address specified in the constructor or initialize is different than `address(0)`.

**Affected source code:**

- [Router.sol:49-51](https://github.com/code-423n4/2022-12-Stealth-Project/blob/fc8589d7d8c1d8488fd97ccc46e1ff11c8426ac2/router-v1/contracts/Router.sol#L49-L51)
- [Position.sol:19](https://github.com/code-423n4/2022-12-Stealth-Project/blob/fc8589d7d8c1d8488fd97ccc46e1ff11c8426ac2/maverick-v1/contracts/models/Position.sol#L19)
- [Position.sol:24](https://github.com/code-423n4/2022-12-Stealth-Project/blob/fc8589d7d8c1d8488fd97ccc46e1ff11c8426ac2/maverick-v1/contracts/models/Position.sol#L24)
- [Pool.sol:80](https://github.dev/code-423n4/2022-12-Stealth-Project/blob/fc8589d7d8c1d8488fd97ccc46e1ff11c8426ac2/maverick-v1/contracts/models/Pool.sol#L80)

## **4. Fix `npm` issues**

> npm audit report

```
To address issues that do not require attention, run:
  npm audit fix
```

### For `maverick` project we found:

```
async  2.0.0 - 2.6.3
Severity: high
Prototype Pollution in async - https://github.com/advisories/GHSA-fwr7-v2mv-hh25
No fix available
```

```
cross-fetch  <=2.2.5 || 3.0.0 - 3.0.5
Severity: moderate
Incorrect Authorization in cross-fetch - https://github.com/advisories/GHSA-7gc6-qh9x-w6h8
Depends on vulnerable versions of node-fetch
fix available via `npm audit fix`
```

```
decode-uri-component  <0.2.1
decode-uri-component vulnerable to Denial of Service (DoS) - https://github.com/advisories/GHSA-w573-4hg7-7wgq
fix available via `npm audit fix`
```

```
elliptic  <6.5.4
Severity: moderate
Use of a Broken or Risky Cryptographic Algorithm - https://github.com/advisories/GHSA-r9p9-mrjm-926w
fix available via `npm audit fix`
```

```
express  <=4.17.2 || 5.0.0-alpha.1 - 5.0.0-alpha.8
Severity: high
qs vulnerable to Prototype Pollution - https://github.com/advisories/GHSA-hrpp-h998-j3pp
Depends on vulnerable versions of body-parser
Depends on vulnerable versions of qs
fix available via `npm audit fix`
```

```
got  <11.8.5
Severity: moderate
Got allows a redirect to a UNIX socket - https://github.com/advisories/GHSA-pfrx-2q88-qq97
No fix available
```

```
json-schema  <0.4.0
Severity: critical
json-schema is vulnerable to Prototype Pollution - https://github.com/advisories/GHSA-896r-f27r-55mw
fix available via `npm audit fix`
```

```
lodash  <=4.17.20
Severity: high
Command Injection in lodash - https://github.com/advisories/GHSA-35jh-r3h4-6jhm
Regular Expression Denial of Service (ReDoS) in lodash - https://github.com/advisories/GHSA-29mw-wpgm-hmr9
fix available via `npm audit fix`
```

```
minimatch  <3.0.5
Severity: high
minimatch ReDoS vulnerability - https://github.com/advisories/GHSA-f8q6-p94x-37v3
No fix available
```

```
minimist  <1.2.6
Severity: critical
Prototype Pollution in minimist - https://github.com/advisories/GHSA-xvch-5gv4-984h
fix available via `npm audit fix`
```

```
node-fetch  <=2.6.6
Severity: high
The `size` option isn't honored after following a redirect in node-fetch - https://github.com/advisories/GHSA-w7rc-rwvf-8q5r
node-fetch is vulnerable to Exposure of Sensitive Information to an Unauthorized Actor - https://github.com/advisories/GHSA-r683-j2x4-v87g
No fix available
```

```
normalize-url  4.3.0 - 4.5.0
Severity: high
ReDoS in normalize-url - https://github.com/advisories/GHSA-px4h-xg32-q955
fix available via `npm audit fix`
```

```
path-parse  <1.0.7
Severity: moderate
Regular Expression Denial of Service in path-parse - https://github.com/advisories/GHSA-hj48-42vr-x3v9
fix available via `npm audit fix`
```

```
qs  6.5.0 - 6.5.2 || 6.7.0 - 6.7.2
Severity: high
qs vulnerable to Prototype Pollution - https://github.com/advisories/GHSA-hrpp-h998-j3pp
qs vulnerable to Prototype Pollution - https://github.com/advisories/GHSA-hrpp-h998-j3pp
fix available via `npm audit fix`
```

```
simple-get  <2.8.2
Severity: high
Exposure of Sensitive Information in simple-get - https://github.com/advisories/GHSA-wpg7-2c88-r8xv
fix available via `npm audit fix`
```

```
tar  <=4.4.17
Severity: high
Arbitrary File Creation/Overwrite on Windows via insufficient relative path sanitization - https://github.com/advisories/GHSA-5955-9wpr-37jh
Arbitrary File Creation/Overwrite via insufficient symlink protection due to directory cache poisoning using symbolic links - https://github.com/advisories/GHSA-qq89-hq3f-393p
Arbitrary File Creation/Overwrite via insufficient symlink protection due to directory cache poisoning using symbolic links - https://github.com/advisories/GHSA-9r2w-394v-53qc
Arbitrary File Creation/Overwrite due to insufficient absolute path sanitization - https://github.com/advisories/GHSA-3jfq-g458-7qm9
Arbitrary File Creation/Overwrite via insufficient symlink protection due to directory cache poisoning - https://github.com/advisories/GHSA-r628-mhmh-qjhw
fix available via `npm audit fix`
```

```
underscore  1.3.2 - 1.12.0
Severity: critical
Arbitrary Code Execution in underscore - https://github.com/advisories/GHSA-cf4h-3jhx-xvhq
No fix available
```

```
ws  5.0.0 - 5.2.2
Severity: moderate
ReDoS in Sec-Websocket-Protocol header - https://github.com/advisories/GHSA-6fc8-4gx4-v693
fix available via `npm audit fix`
```

```
yargs-parser  <=5.0.0
Severity: moderate
yargs-parser Vulnerable to Prototype Pollution - https://github.com/advisories/GHSA-p9pc-299p-vxgp
No fix available
```

```
62 vulnerabilities (8 low, 16 moderate, 14 high, 24 critical)
```

### For `router` project we found:

```
async  2.0.0 - 2.6.3
Severity: high
Prototype Pollution in async - https://github.com/advisories/GHSA-fwr7-v2mv-hh25
No fix available
```

```
cross-fetch  <=2.2.5 || 3.0.0 - 3.0.5
Severity: moderate
Incorrect Authorization in cross-fetch - https://github.com/advisories/GHSA-7gc6-qh9x-w6h8
Depends on vulnerable versions of node-fetch
fix available via `npm audit fix`
```

```
decode-uri-component  <0.2.1
decode-uri-component vulnerable to Denial of Service (DoS) - https://github.com/advisories/GHSA-w573-4hg7-7wgq
fix available via `npm audit fix`
```

```
elliptic  <6.5.4
Severity: moderate
Use of a Broken or Risky Cryptographic Algorithm - https://github.com/advisories/GHSA-r9p9-mrjm-926w
fix available via `npm audit fix`
```

```
express  <=4.17.2 || 5.0.0-alpha.1 - 5.0.0-alpha.8
Severity: high
qs vulnerable to Prototype Pollution - https://github.com/advisories/GHSA-hrpp-h998-j3pp
Depends on vulnerable versions of body-parser
Depends on vulnerable versions of qs
fix available via `npm audit fix`
```

```
got  <11.8.5
Severity: moderate
Got allows a redirect to a UNIX socket - https://github.com/advisories/GHSA-pfrx-2q88-qq97
No fix available
```

```
json-schema  <0.4.0
Severity: critical
json-schema is vulnerable to Prototype Pollution - https://github.com/advisories/GHSA-896r-f27r-55mw
fix available via `npm audit fix`
```

```
lodash  <=4.17.20
Severity: high
Command Injection in lodash - https://github.com/advisories/GHSA-35jh-r3h4-6jhm
Regular Expression Denial of Service (ReDoS) in lodash - https://github.com/advisories/GHSA-29mw-wpgm-hmr9
fix available via `npm audit fix`
```

```
minimatch  <3.0.5
Severity: high
minimatch ReDoS vulnerability - https://github.com/advisories/GHSA-f8q6-p94x-37v3
No fix available
```

```
minimist  <1.2.6
Severity: critical
Prototype Pollution in minimist - https://github.com/advisories/GHSA-xvch-5gv4-984h
fix available via `npm audit fix`
```

```
node-fetch  <=2.6.6
Severity: high
The `size` option isn't honored after following a redirect in node-fetch - https://github.com/advisories/GHSA-w7rc-rwvf-8q5r
node-fetch is vulnerable to Exposure of Sensitive Information to an Unauthorized Actor - https://github.com/advisories/GHSA-r683-j2x4-v87g
No fix available
```

```
normalize-url  4.3.0 - 4.5.0
Severity: high
ReDoS in normalize-url - https://github.com/advisories/GHSA-px4h-xg32-q955
fix available via `npm audit fix`
```

```
path-parse  <1.0.7
Severity: moderate
Regular Expression Denial of Service in path-parse - https://github.com/advisories/GHSA-hj48-42vr-x3v9
fix available via `npm audit fix`
```

```
qs  6.5.0 - 6.5.2 || 6.7.0 - 6.7.2
Severity: high
qs vulnerable to Prototype Pollution - https://github.com/advisories/GHSA-hrpp-h998-j3pp
qs vulnerable to Prototype Pollution - https://github.com/advisories/GHSA-hrpp-h998-j3pp
fix available via `npm audit fix`
```

```
simple-get  <2.8.2
Severity: high
Exposure of Sensitive Information in simple-get - https://github.com/advisories/GHSA-wpg7-2c88-r8xv
fix available via `npm audit fix`
```

```
tar  <=4.4.17
Severity: high
Arbitrary File Creation/Overwrite on Windows via insufficient relative path sanitization - https://github.com/advisories/GHSA-5955-9wpr-37jh
Arbitrary File Creation/Overwrite via insufficient symlink protection due to directory cache poisoning using symbolic links - https://github.com/advisories/GHSA-qq89-hq3f-393p
Arbitrary File Creation/Overwrite via insufficient symlink protection due to directory cache poisoning using symbolic links - https://github.com/advisories/GHSA-9r2w-394v-53qc
Arbitrary File Creation/Overwrite due to insufficient absolute path sanitization - https://github.com/advisories/GHSA-3jfq-g458-7qm9
Arbitrary File Creation/Overwrite via insufficient symlink protection due to directory cache poisoning - https://github.com/advisories/GHSA-r628-mhmh-qjhw
fix available via `npm audit fix`
```

```
underscore  1.3.2 - 1.12.0
Severity: critical
Arbitrary Code Execution in underscore - https://github.com/advisories/GHSA-cf4h-3jhx-xvhq
No fix available
```

```
ws  5.0.0 - 5.2.2
Severity: moderate
ReDoS in Sec-Websocket-Protocol header - https://github.com/advisories/GHSA-6fc8-4gx4-v693
fix available via `npm audit fix`
```

```
yargs-parser  <=5.0.0
Severity: moderate
yargs-parser Vulnerable to Prototype Pollution - https://github.com/advisories/GHSA-p9pc-299p-vxgp
No fix available
```

```
62 vulnerabilities (8 low, 16 moderate, 14 high, 24 critical)
```

---

## **5. Integer overflow by unsafe casting**

Keep in mind that the version of solidity used, despite being greater than `0.8`, does not prevent integer overflows during casting, it only does so in mathematical operations.

It is necessary to safely convert between the different numeric types.

**Recommendation:**

Use a [safeCast](https://docs.openzeppelin.com/contracts/3.x/api/utils#SafeCast) from Open Zeppelin.

```javascript
twa.updateValue(currentState.activeTick * PRBMathSD59x18.SCALE + int256(Math.clip(delta.endSqrtPrice, delta.sqrtLowerTickPrice).div(delta.sqrtUpperTickPrice - delta.sqrtLowerTickPrice)));
```

**Affected source code:**

- [BinMap.sol:21](https://github.com/code-423n4/2022-12-Stealth-Project/blob/fc8589d7d8c1d8488fd97ccc46e1ff11c8426ac2/maverick-v1/contracts/libraries/BinMap.sol#L21)
- [BinMap.sol:26](https://github.com/code-423n4/2022-12-Stealth-Project/blob/fc8589d7d8c1d8488fd97ccc46e1ff11c8426ac2/maverick-v1/contracts/libraries/BinMap.sol#L26)
- [BinMap.sol:31](https://github.com/code-423n4/2022-12-Stealth-Project/blob/fc8589d7d8c1d8488fd97ccc46e1ff11c8426ac2/maverick-v1/contracts/libraries/BinMap.sol#L31)
- [BinMap.sol:45](https://github.com/code-423n4/2022-12-Stealth-Project/blob/fc8589d7d8c1d8488fd97ccc46e1ff11c8426ac2/maverick-v1/contracts/libraries/BinMap.sol#L45)
- [BinMap.sol:56](https://github.com/code-423n4/2022-12-Stealth-Project/blob/fc8589d7d8c1d8488fd97ccc46e1ff11c8426ac2/maverick-v1/contracts/libraries/BinMap.sol#L56)
- [BinMap.sol:88](https://github.com/code-423n4/2022-12-Stealth-Project/blob/fc8589d7d8c1d8488fd97ccc46e1ff11c8426ac2/maverick-v1/contracts/libraries/BinMap.sol#L88)
- [BinMap.sol:101](https://github.com/code-423n4/2022-12-Stealth-Project/blob/fc8589d7d8c1d8488fd97ccc46e1ff11c8426ac2/maverick-v1/contracts/libraries/BinMap.sol#L101)
- [BinMath.sol:92](https://github.com/code-423n4/2022-12-Stealth-Project/blob/fc8589d7d8c1d8488fd97ccc46e1ff11c8426ac2/maverick-v1/contracts/libraries/BinMath.sol#L92)
- [Cast.sol:6](https://github.com/code-423n4/2022-12-Stealth-Project/blob/fc8589d7d8c1d8488fd97ccc46e1ff11c8426ac2/maverick-v1/contracts/libraries/Cast.sol#L6)
- [Math.sol:69](https://github.com/code-423n4/2022-12-Stealth-Project/blob/fc8589d7d8c1d8488fd97ccc46e1ff11c8426ac2/maverick-v1/contracts/libraries/Math.sol#L69)
- [Twa.sol:12](https://github.com/code-423n4/2022-12-Stealth-Project/blob/fc8589d7d8c1d8488fd97ccc46e1ff11c8426ac2/maverick-v1/contracts/libraries/Twa.sol#L12)
- [Twa.sol:13](https://github.com/code-423n4/2022-12-Stealth-Project/blob/fc8589d7d8c1d8488fd97ccc46e1ff11c8426ac2/maverick-v1/contracts/libraries/Twa.sol#L13)
- [Twa.sol:14](https://github.com/code-423n4/2022-12-Stealth-Project/blob/fc8589d7d8c1d8488fd97ccc46e1ff11c8426ac2/maverick-v1/contracts/libraries/Twa.sol#L14)
- [Twa.sol:18](https://github.com/code-423n4/2022-12-Stealth-Project/blob/fc8589d7d8c1d8488fd97ccc46e1ff11c8426ac2/maverick-v1/contracts/libraries/Twa.sol#L18)
- [Twa.sol:26](https://github.com/code-423n4/2022-12-Stealth-Project/blob/fc8589d7d8c1d8488fd97ccc46e1ff11c8426ac2/maverick-v1/contracts/libraries/Twa.sol#L26)
- [Twa.sol:27](https://github.com/code-423n4/2022-12-Stealth-Project/blob/fc8589d7d8c1d8488fd97ccc46e1ff11c8426ac2/maverick-v1/contracts/libraries/Twa.sol#L27)
- [Twa.sol:36](https://github.com/code-423n4/2022-12-Stealth-Project/blob/fc8589d7d8c1d8488fd97ccc46e1ff11c8426ac2/maverick-v1/contracts/libraries/Twa.sol#L36)
- [Factory.sol:62](https://github.com/code-423n4/2022-12-Stealth-Project/blob/fc8589d7d8c1d8488fd97ccc46e1ff11c8426ac2/maverick-v1/contracts/models/Factory.sol#L62)
- [Pool.sol:294](https://github.com/code-423n4/2022-12-Stealth-Project/blob/fc8589d7d8c1d8488fd97ccc46e1ff11c8426ac2/maverick-v1/contracts/models/Pool.sol#L294)
- [Pool.sol:453](https://github.com/code-423n4/2022-12-Stealth-Project/blob/fc8589d7d8c1d8488fd97ccc46e1ff11c8426ac2/maverick-v1/contracts/models/Pool.sol#L453)
- [Pool.sol:455](https://github.com/code-423n4/2022-12-Stealth-Project/blob/fc8589d7d8c1d8488fd97ccc46e1ff11c8426ac2/maverick-v1/contracts/models/Pool.sol#L455)
- [Pool.sol:470](https://github.com/code-423n4/2022-12-Stealth-Project/blob/fc8589d7d8c1d8488fd97ccc46e1ff11c8426ac2/maverick-v1/contracts/models/Pool.sol#L470)
- [Pool.sol:472](https://github.com/code-423n4/2022-12-Stealth-Project/blob/fc8589d7d8c1d8488fd97ccc46e1ff11c8426ac2/maverick-v1/contracts/models/Pool.sol#L472)
- [Pool.sol:550](https://github.com/code-423n4/2022-12-Stealth-Project/blob/fc8589d7d8c1d8488fd97ccc46e1ff11c8426ac2/maverick-v1/contracts/models/Pool.sol#L550)

# Non critical

## **6. Start with the token Id 0**

The id 0 is a valid valid id and the current logic starts the ERC721 with 1, it is convenient to study if it is more convenient to start with the id 0.

**Affected source code:**

- [Position.sol:20](https://github.com/code-423n4/2022-12-Stealth-Project/blob/fc8589d7d8c1d8488fd97ccc46e1ff11c8426ac2/maverick-v1/contracts/models/Position.sol#L20)

## **7. Use libraries instead of abstract contracts**

The logic of the functions detailed below have been defined within an abstract contract instead of in a library, in order to reduce inheritance and ease readability, as well as update gap issues (in case of happened), it is convenient. use libraries to define reusable functions.

**Affected source code:**

- [Deadline.sol:4](https://github.com/code-423n4/2022-12-Stealth-Project/blob/fc8589d7d8c1d8488fd97ccc46e1ff11c8426ac2/router-v1/contracts/libraries/Deadline.sol#L4)
- [Multicall.sol:9](https://github.com/code-423n4/2022-12-Stealth-Project/blob/fc8589d7d8c1d8488fd97ccc46e1ff11c8426ac2/router-v1/contracts/libraries/Multicall.sol#L9)
- [SelfPermit.sol:14](https://github.com/code-423n4/2022-12-Stealth-Project/blob/fc8589d7d8c1d8488fd97ccc46e1ff11c8426ac2/router-v1/contracts/libraries/SelfPermit.sol#L14)

## **8. Unify design**

The Owner logic has been created entirely in the `Factory` contract, however in other contracts such as `PositionMetadata` or `Position` it extends from `Ownable`, the design of the contracts should be unified.

**Affected source code:**

- [Factory.sol:24](https://github.com/code-423n4/2022-12-Stealth-Project/blob/fc8589d7d8c1d8488fd97ccc46e1ff11c8426ac2/maverick-v1/contracts/models/Factory.sol#L24)
