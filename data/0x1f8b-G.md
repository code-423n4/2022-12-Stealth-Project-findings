- [Gas](#gas)
    - [**1. Avoid Counters.Counter library**](#1-avoid-counterscounter-library)
        - [Total gas saved: **47**](#total-gas-saved-47)
        - [Total gas saved: **10**](#total-gas-saved-10)
    - [**2. Use calldata instead of memory**](#2-use-calldata-instead-of-memory)
    - [**3. There's no need to set default values for variables**](#3-theres-no-need-to-set-default-values-for-variables)
        - [Total gas saved: **8**](#total-gas-saved-8)
    - [**4. Reduce the size of error messages Long revert Strings**](#4-reduce-the-size-of-error-messages-long-revert-strings)
        - [Total gas saved: **18 * 5 = 90**](#total-gas-saved-18--5--90)
    - [**5. delete optimization**](#5-delete-optimization)
        - [Total gas saved: **5**](#total-gas-saved-5)
    - [**6. Avoid compound assignment operator in state variables**](#6-avoid-compound-assignment-operator-in-state-variables)
        - [Total gas saved: **13 * 20 = 260**](#total-gas-saved-13--20--260)
    - [**7. Reorder structure layout**](#7-reorder-structure-layout)

# Gas


## **1. Avoid `Counters.Counter` library**

`Counter` library only increase and return a regular counter, it's cheaper to use a regular variable and `+=`.

**Affected source code:**

- [Position.sol:13](https://github.dev/code-423n4/2022-12-Stealth-Project/blob/fc8589d7d8c1d8488fd97ccc46e1ff11c8426ac2/maverick-v1/contracts/models/Position.sol#L13)

```
·--------------------------------------------|---------------------------|------------|-----------------------------·
|            Solc version: 0.8.17            ·  Optimizer enabled: true  ·  Runs: 80  ·  Block limit: 30000000 gas  │
·············································|···························|············|······························
|  Methods                                                                                                          │
·····················|·······················|·············|·············|············|···············|·············|
  Position           ·  mint - before        ·     145626  ·     153726  ·    148126  ·           45  ·          -  │
  Position           ·  mint - after         ·     145579  ·     153679  ·    147889  ·           39  ·          -  │
·····················|·······················|·············|·············|············|···············|·············|
|  Improve                                                                                                          │
·····················|·······················|·············|·············|············|···············|·············|
                                             ·         47  ·         47  ·                                          │
·····················|·······················|·············|·············|············|···············|·············|
```

> Note: The deploy should also be affected

### Total gas saved: **47**

> npm run size

```
Position Before: 6156
Position After: 6146
```

### Total gas saved: **10**

## **2. Use `calldata` instead of `memory`**

> Note that this entry was not detected in [known issues](https://gist.github.com/Picodes/4d46aae8efe6935f99beba012aa17876)

Some methods are declared as `external` but the arguments are defined as `memory` instead of as `calldata`.

By marking the function as `external` it is possible to use `calldata` in the arguments shown below and save significant gas.

**Recommended change:**

```diff
-   function exactInput(ExactInputParams memory params) external payable override checkDeadline(params.deadline) returns (uint256 amountOut) {
+   function exactInput(ExactInputParams calldata params) external payable override checkDeadline(params.deadline) returns (uint256 amountOut) {
```

**Affected source code:**

- [Router.sol:143-166](https://github.com/code-423n4/2022-12-Stealth-Project/blob/fc8589d7d8c1d8488fd97ccc46e1ff11c8426ac2/router-v1/contracts/Router.sol#L143-L166)

## **3. There's no need to set default values for variables**

> Note that this entry was not detected in [known issues](https://gist.github.com/Picodes/4d46aae8efe6935f99beba012aa17876)

If a variable is not set/initialized, the default value is assumed (0, `false`, 0x0 ... depending on the data type). You are simply wasting gas if you directly initialize it with its default value.

**Proof of concept (*without optimizations*):**

```javascript
pragma solidity 0.8.15;

contract TesterA {
function testInit() public view returns (uint) { uint256 a = 0; return a; }
}

contract TesterB {
function testNoInit() public view returns (uint) { uint256 a; return a; }
}
```

Gas saving executing: **8 per entry**

```
TesterA.testInit:   21392
TesterB.testNoInit: 21384
```

**Affected source code:**

- [BinMath.sol:14](https://github.com/code-423n4/2022-12-Stealth-Project/blob/fc8589d7d8c1d8488fd97ccc46e1ff11c8426ac2/maverick-v1/contracts/libraries/BinMath.sol#L14)

### Total gas saved: **8**

## **4. Reduce the size of error messages (Long revert Strings)**

Shortening revert strings to fit in 32 bytes will decrease deployment time gas and will decrease runtime gas when the revert condition is met.

Revert strings that are longer than 32 bytes require at least one additional mstore, along with additional overhead for computing memory offset, etc.

**Proof of concept (*without optimizations*):**

```javascript
pragma solidity 0.8.15;

contract TesterA {
function testShortRevert(bool path) public view {
require(path, "test error");
}
}

contract TesterB {
function testLongRevert(bool path) public view {
require(path, "test big error message, more than 32 bytes");
}
}
```

Gas saving executing: **18 per entry**

```
TesterA.testShortRevert: 21886
TesterB.testLongRevert:  21904
```

**Affected source code:**

- [Factory.sol:27](https://github.com/code-423n4/2022-12-Stealth-Project/blob/fc8589d7d8c1d8488fd97ccc46e1ff11c8426ac2/maverick-v1/contracts/models/Factory.sol#L27)
- [Factory.sol:35](https://github.com/code-423n4/2022-12-Stealth-Project/blob/fc8589d7d8c1d8488fd97ccc46e1ff11c8426ac2/maverick-v1/contracts/models/Factory.sol#L35)
- [Factory.sol:59](https://github.com/code-423n4/2022-12-Stealth-Project/blob/fc8589d7d8c1d8488fd97ccc46e1ff11c8426ac2/maverick-v1/contracts/models/Factory.sol#L59)
- [Factory.sol:61](https://github.com/code-423n4/2022-12-Stealth-Project/blob/fc8589d7d8c1d8488fd97ccc46e1ff11c8426ac2/maverick-v1/contracts/models/Factory.sol#L61)
- [TestPool.sol:68](https://github.com/code-423n4/2022-12-Stealth-Project/blob/fc8589d7d8c1d8488fd97ccc46e1ff11c8426ac2/maverick-v1/contracts/test/TestPool.sol#L68)

### Total gas saved: **18 * 5 = 90**

## **5. `delete` optimization**

Use `delete` instead of set to default value (`false` or `0`).

5 gas could be saved per entry in the following affected lines:

**Affected source code:**

- [Pool.sol:359](https://github.com/code-423n4/2022-12-Stealth-Project/blob/fc8589d7d8c1d8488fd97ccc46e1ff11c8426ac2/maverick-v1/contracts/models/Pool.sol#L359)

### Total gas saved: **5**

## **6. Avoid compound assignment operator in state variables**

Using compound assignment operators for state variables (like `State += X` or `State -= X` ...) it's more expensive than using operator assignment (like `State = State + X` or `State = State - X` ...).

**Proof of concept (*without optimizations*):**

```javascript
pragma solidity 0.8.15;

contract TesterA {
uint256 private _a;
function testShort() public {
_a += 1;
}
}

contract TesterB {
Uint256 private _a;
function testLong() public {
_a = _a + 1;
}
}
```

Gas saving executing: **13 per entry**

```
TesterA.testShort: 43507
TesterB.testLong:  43494
```

**Affected source code:**

- [Bin.sol:67](https://github.com/code-423n4/2022-12-Stealth-Project/blob/fc8589d7d8c1d8488fd97ccc46e1ff11c8426ac2/maverick-v1/contracts/libraries/Bin.sol#L67)
- [Bin.sol:70](https://github.com/code-423n4/2022-12-Stealth-Project/blob/fc8589d7d8c1d8488fd97ccc46e1ff11c8426ac2/maverick-v1/contracts/libraries/Bin.sol#L70)
- [Bin.sol:88](https://github.com/code-423n4/2022-12-Stealth-Project/blob/fc8589d7d8c1d8488fd97ccc46e1ff11c8426ac2/maverick-v1/contracts/libraries/Bin.sol#L88)
- [Bin.sol:89](https://github.com/code-423n4/2022-12-Stealth-Project/blob/fc8589d7d8c1d8488fd97ccc46e1ff11c8426ac2/maverick-v1/contracts/libraries/Bin.sol#L89)
- [Bin.sol:90](https://github.com/code-423n4/2022-12-Stealth-Project/blob/fc8589d7d8c1d8488fd97ccc46e1ff11c8426ac2/maverick-v1/contracts/libraries/Bin.sol#L90)
- [Bin.sol:91](https://github.com/code-423n4/2022-12-Stealth-Project/blob/fc8589d7d8c1d8488fd97ccc46e1ff11c8426ac2/maverick-v1/contracts/libraries/Bin.sol#L91)
- [Bin.sol:142](https://github.com/code-423n4/2022-12-Stealth-Project/blob/fc8589d7d8c1d8488fd97ccc46e1ff11c8426ac2/maverick-v1/contracts/libraries/Bin.sol#L142)
- [Bin.sol:144](https://github.com/code-423n4/2022-12-Stealth-Project/blob/fc8589d7d8c1d8488fd97ccc46e1ff11c8426ac2/maverick-v1/contracts/libraries/Bin.sol#L144)
- [Bin.sol:148](https://github.com/code-423n4/2022-12-Stealth-Project/blob/fc8589d7d8c1d8488fd97ccc46e1ff11c8426ac2/maverick-v1/contracts/libraries/Bin.sol#L148)
- [Bin.sol:154](https://github.com/code-423n4/2022-12-Stealth-Project/blob/fc8589d7d8c1d8488fd97ccc46e1ff11c8426ac2/maverick-v1/contracts/libraries/Bin.sol#L154)
- [Bin.sol:157](https://github.com/code-423n4/2022-12-Stealth-Project/blob/fc8589d7d8c1d8488fd97ccc46e1ff11c8426ac2/maverick-v1/contracts/libraries/Bin.sol#L157)
- [Delta.sol:24](https://github.com/code-423n4/2022-12-Stealth-Project/blob/fc8589d7d8c1d8488fd97ccc46e1ff11c8426ac2/maverick-v1/contracts/libraries/Delta.sol#L24)
- [Delta.sol:25](https://github.com/code-423n4/2022-12-Stealth-Project/blob/fc8589d7d8c1d8488fd97ccc46e1ff11c8426ac2/maverick-v1/contracts/libraries/Delta.sol#L25)
- [Delta.sol:26](https://github.com/code-423n4/2022-12-Stealth-Project/blob/fc8589d7d8c1d8488fd97ccc46e1ff11c8426ac2/maverick-v1/contracts/libraries/Delta.sol#L26)
- [Pool.sol:161](https://github.com/code-423n4/2022-12-Stealth-Project/blob/fc8589d7d8c1d8488fd97ccc46e1ff11c8426ac2/maverick-v1/contracts/models/Pool.sol#L161)
- [Pool.sol:162](https://github.com/code-423n4/2022-12-Stealth-Project/blob/fc8589d7d8c1d8488fd97ccc46e1ff11c8426ac2/maverick-v1/contracts/models/Pool.sol#L162)
- [Pool.sol:199](https://github.com/code-423n4/2022-12-Stealth-Project/blob/fc8589d7d8c1d8488fd97ccc46e1ff11c8426ac2/maverick-v1/contracts/models/Pool.sol#L199)
- [Pool.sol:200](https://github.com/code-423n4/2022-12-Stealth-Project/blob/fc8589d7d8c1d8488fd97ccc46e1ff11c8426ac2/maverick-v1/contracts/models/Pool.sol#L200)
- [Pool.sol:288](https://github.com/code-423n4/2022-12-Stealth-Project/blob/fc8589d7d8c1d8488fd97ccc46e1ff11c8426ac2/maverick-v1/contracts/models/Pool.sol#L288)
- [Pool.sol:291](https://github.com/code-423n4/2022-12-Stealth-Project/blob/fc8589d7d8c1d8488fd97ccc46e1ff11c8426ac2/maverick-v1/contracts/models/Pool.sol#L291)

### Total gas saved: **13 * 20 = 260**

## **7. Reorder structure layout**

The following structures could be optimized moving the position of certain values in order to save a some storage slots:

`Instance` in [Delta.sol:5](https://github.dev/code-423n4/2022-12-Stealth-Project/blob/fc8589d7d8c1d8488fd97ccc46e1ff11c8426ac2/maverick-v1/contracts/libraries/Delta.sol#L5)

```diff
    struct Instance {
        uint256 deltaInBinInternal;
        uint256 deltaInErc;
        uint256 deltaOutErc;
        uint256 excess;
-       bool tokenAIn;
        uint256 endSqrtPrice;
+       bool tokenAIn;
        bool exactOutput;
        bool swappedToMaxPrice;
        bool skipCombine;
        bool decrementTick;
        uint256 sqrtPriceLimit;
        uint256 sqrtLowerTickPrice;
        uint256 sqrtUpperTickPrice;
        uint256 sqrtPrice;
    }
```

**Reference:**

- https://ethdebug.github.io/solidity-data-representation

> Enums are represented by integers; the possibility listed first by 0, the next by 1, and so forth. An enum type just acts like uintN, where N is the smallest legal value large enough to accomodate all the possibilities.

- https://docs.soliditylang.org/en/v0.8.17/internals/layout_in_storage.html#storage-inplace-encoding
