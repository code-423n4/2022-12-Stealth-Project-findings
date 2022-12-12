# Index
1. I = I + (-) X is cheaper in gas cost than I += (-=) X
2. Use require instead of &&
3. Require / Revert strings longer than 32 bytes cost extra gas
4. Use unchecked in for/while loops when it's not possible to overflow

# Details
## 1. I = I + (-) X is cheaper in gas cost than I += (-=) X
### Description

Use of I = I + X is cheaper in gas cost than I += X when a state variable is used. 
The mainly function where it's possible to  apply this optimization is in swap() & addLiquidity().
As you can see in the PoC there are **114 gas saved** running the TestPool - addLiquidity 
and **192 gas saved** running the TestPool - swap. 

### PoC
The following PoC has been run using the sponsor test.

**Running test affected before change the code**
·--------------------------------------------|---------------------------|------------|-----------------------------·
|            Solc version: 0.8.17            ·  Optimizer enabled: true  ·  Runs: 80  ·  Block limit: 30000000 gas  ¦
·············································|···························|············|······························
|  Methods                                                                                                          ¦
·····················|·······················|·············|·············|············|···············|··············
|  Contract          ·  Method               ·  Min        ·  Max        ·  Avg       ·  # calls      ·  eur (avg)  ¦
·····················|·······················|·············|·············|············|···············|··············
|  TestPool          ·  addLiquidity         ·     131281  ·    2742205  ·    397519  ·          304  ·          -  ¦
·····················|·······················|·············|·············|············|···············|··············
|  TestPool          ·  swap                 ·      79795  ·     300404  ·    193832  ·          473  ·          -  ¦
·--------------------------------------------|-------------|-------------|------------|---------------|-------------·

**Running test affected after change the code**
·--------------------------------------------|---------------------------|------------|-----------------------------·
|            Solc version: 0.8.17            ·  Optimizer enabled: true  ·  Runs: 80  ·  Block limit: 30000000 gas  │
·············································|···························|············|······························
|  Methods                                                                                                          │
·····················|·······················|·············|·············|············|···············|··············
|  Contract          ·  Method               ·  Min        ·  Max        ·  Avg       ·  # calls      ·  eur (avg)  │
·····················|·······················|·············|·············|············|···············|··············
|  TestPool          ·  addLiquidity         ·     131167  ·    2742091  ·    397405  ·          304  ·          -  │
·····················|·······················|·············|·············|············|···············|··············
|  TestPool          ·  swap                 ·      79795  ·     300205  ·    193640  ·          473  ·          -  │
·--------------------------------------------|-------------|-------------|------------|---------------|-------------·

### Lines in the code
[Pool.sol#L161](https://github.com/code-423n4/2022-12-Stealth-Project/blob/fc8589d7d8c1d8488fd97ccc46e1ff11c8426ac2/maverick-v1/contracts/models/Pool.sol#L161)
[Pool.sol#L162](https://github.com/code-423n4/2022-12-Stealth-Project/blob/fc8589d7d8c1d8488fd97ccc46e1ff11c8426ac2/maverick-v1/contracts/models/Pool.sol#L162)
[Pool.sol#L288](https://github.com/code-423n4/2022-12-Stealth-Project/blob/fc8589d7d8c1d8488fd97ccc46e1ff11c8426ac2/maverick-v1/contracts/models/Pool.sol#L288)
[Pool.sol#L291](https://github.com/code-423n4/2022-12-Stealth-Project/blob/fc8589d7d8c1d8488fd97ccc46e1ff11c8426ac2/maverick-v1/contracts/models/Pool.sol#L291)

## 2. Use require instead of &&
### Description
Split of conditions of an require sentence in different requires sentences can save gas. 
As we can see in the PoC below there is an average of 10-15 gas saved.

### PoC (Pool.sol and Factory.sol)
**Running test affected before change the code**
·--------------------------------------------|---------------------------|------------|-----------------------------·
|            Solc version: 0.8.17            ·  Optimizer enabled: true  ·  Runs: 80  ·  Block limit: 30000000 gas  ¦
·············································|···························|············|······························
|  Methods                                                                                                          ¦
·····················|·······················|·············|·············|············|···············|··············
|  Contract          ·  Method               ·  Min        ·  Max        ·  Avg       ·  # calls      ·  eur (avg)  ¦
·····················|·······················|·············|·············|············|···············|··············
|  Factory           ·  setOwner             ·          -  ·          -  ·     28068  ·            1  ·          -  ¦
·····················|·······················|·············|·············|············|···············|··············
|  Pool              ·  adminAction          ·      33257  ·      52733  ·     48345  ·           52  ·          -  ¦
·····················|·······················|·············|·············|············|···············|··············
|  Pool              ·  migrateBinsUpStack   ·      29855  ·     414505  ·     47785  ·           45  ·          -  ¦
·····················|·······················|·············|·············|············|···············|··············
|  Pool              ·  removeLiquidity      ·      48670  ·     100318  ·     82109  ·           46  ·          -  ¦
·····················|·······················|·············|·············|············|···············|··············
|  Pool              ·  transferLiquidity    ·          -  ·          -  ·     57927  ·            1  ·          -  ¦
·····················|·······················|·············|·············|············|···············|··············
|  TestPool          ·  addLiquidity         ·     131281  ·    2742205  ·    397519  ·          304  ·          -  ¦
·····················|·······················|·············|·············|············|···············|··············
|  TestPool          ·  swap                 ·      79795  ·     300404  ·    193832  ·          473  ·          -  ¦
·····················|·······················|·············|·············|············|···············|··············
|  TestPool          ·  swapWLimit           ·      93308  ·     135367  ·    117858  ·           62  ·          -  ¦
·--------------------------------------------|-------------|-------------|------------|---------------|-------------·

**Running test affected after change the code**
·--------------------------------------------|---------------------------|------------|-----------------------------·
|            Solc version: 0.8.17            ·  Optimizer enabled: true  ·  Runs: 80  ·  Block limit: 30000000 gas  │
·············································|···························|············|······························
|  Methods                                                                                                          │
·····················|·······················|·············|·············|············|···············|··············
|  Contract          ·  Method               ·  Min        ·  Max        ·  Avg       ·  # calls      ·  eur (avg)  │
·····················|·······················|·············|·············|············|···············|··············
|  Factory           ·  setOwner             ·          -  ·          -  ·     28054  ·            1  ·          -  │
·····················|·······················|·············|·············|············|···············|··············
|  Pool              ·  adminAction          ·      33250  ·      52726  ·     48338  ·           52  ·          -  │
·····················|·······················|·············|·············|············|···············|··············
|  Pool              ·  migrateBinsUpStack   ·      29848  ·     414498  ·     47778  ·           45  ·          -  │
·····················|·······················|·············|·············|············|···············|··············
|  Pool              ·  removeLiquidity      ·      48663  ·     100311  ·     82103  ·           46  ·          -  │
·····················|·······················|·············|·············|············|···············|··············
|  Pool              ·  transferLiquidity    ·          -  ·          -  ·     57920  ·            1  ·          -  │
·····················|·······················|·············|·············|············|···············|··············
|  TestPool          ·  addLiquidity         ·     131266  ·    2742190  ·    397504  ·          304  ·          -  │
·····················|·······················|·············|·············|············|···············|··············
|  TestPool          ·  swap                 ·      79788  ·     300397  ·    193825  ·          473  ·          -  │
·····················|·······················|·············|·············|············|···············|··············
|  TestPool          ·  swapWLimit           ·      93301  ·     135360  ·    117851  ·           62  ·          -  │
·--------------------------------------------|-------------|-------------|------------|---------------|-------------·



### Lines in the code
[Pool.sol#L93](https://github.com/code-423n4/2022-12-Stealth-Project/blob/fc8589d7d8c1d8488fd97ccc46e1ff11c8426ac2/maverick-v1/contracts/models/Pool.sol#L93)
[Pool.sol#L168](https://github.com/code-423n4/2022-12-Stealth-Project/blob/fc8589d7d8c1d8488fd97ccc46e1ff11c8426ac2/maverick-v1/contracts/models/Pool.sol#L168)
[Factory.sol#L41](https://github.com/code-423n4/2022-12-Stealth-Project/blob/fc8589d7d8c1d8488fd97ccc46e1ff11c8426ac2/maverick-v1/contracts/models/Factory.sol#L41)
[Factory.sol#L60](https://github.com/code-423n4/2022-12-Stealth-Project/blob/fc8589d7d8c1d8488fd97ccc46e1ff11c8426ac2/maverick-v1/contracts/models/Factory.sol#L60)
[Factory.sol#L62](https://github.com/code-423n4/2022-12-Stealth-Project/blob/fc8589d7d8c1d8488fd97ccc46e1ff11c8426ac2/maverick-v1/contracts/models/Factory.sol#L62)

## 3. Require / Revert strings longer than 32 bytes cost extra gas
### Description
Each extra memory word of bytes past the original 32 incurs an MSTORE which costs 3 gas

### Lines in the code
[Factory.sol#L27](https://github.com/code-423n4/2022-12-Stealth-Project/blob/fc8589d7d8c1d8488fd97ccc46e1ff11c8426ac2/maverick-v1/contracts/models/Factory.sol#L27)
[Factory.sol#L35](https://github.com/code-423n4/2022-12-Stealth-Project/blob/fc8589d7d8c1d8488fd97ccc46e1ff11c8426ac2/maverick-v1/contracts/models/Factory.sol#L35)
[Factory.sol#L59](https://github.com/code-423n4/2022-12-Stealth-Project/blob/fc8589d7d8c1d8488fd97ccc46e1ff11c8426ac2/maverick-v1/contracts/models/Factory.sol#L59)
[Factory.sol#L61](https://github.com/code-423n4/2022-12-Stealth-Project/blob/fc8589d7d8c1d8488fd97ccc46e1ff11c8426ac2/maverick-v1/contracts/models/Factory.sol#L61)

## 4. Use unchecked in for/while loops when it's not possible to overflow
### Description
Use unchecked { i++; } or unchecked{ ++i; } when it's not possible to overflow to save gas.

### Lines in the code
[Multicall.sol#L13](https://github.com/code-423n4/2022-12-Stealth-Project/blob/fc8589d7d8c1d8488fd97ccc46e1ff11c8426ac2/router-v1/contracts/libraries/Multicall.sol#L13)
[Pool.sol#L127](https://github.com/code-423n4/2022-12-Stealth-Project/blob/fc8589d7d8c1d8488fd97ccc46e1ff11c8426ac2/maverick-v1/contracts/models/Pool.sol#L127)
[Pool.sol#L180](https://github.com/code-423n4/2022-12-Stealth-Project/blob/fc8589d7d8c1d8488fd97ccc46e1ff11c8426ac2/maverick-v1/contracts/models/Pool.sol#L180)
[Pool.sol#L193](https://github.com/code-423n4/2022-12-Stealth-Project/blob/fc8589d7d8c1d8488fd97ccc46e1ff11c8426ac2/maverick-v1/contracts/models/Pool.sol#L193)
[Pool.sol#L224](https://github.com/code-423n4/2022-12-Stealth-Project/blob/fc8589d7d8c1d8488fd97ccc46e1ff11c8426ac2/maverick-v1/contracts/models/Pool.sol#L224)
[Pool.sol#L380](https://github.com/code-423n4/2022-12-Stealth-Project/blob/fc8589d7d8c1d8488fd97ccc46e1ff11c8426ac2/maverick-v1/contracts/models/Pool.sol#L380)
[Pool.sol#L389](https://github.com/code-423n4/2022-12-Stealth-Project/blob/fc8589d7d8c1d8488fd97ccc46e1ff11c8426ac2/maverick-v1/contracts/models/Pool.sol#L389)
[Pool.sol#L414](https://github.com/code-423n4/2022-12-Stealth-Project/blob/fc8589d7d8c1d8488fd97ccc46e1ff11c8426ac2/maverick-v1/contracts/models/Pool.sol#L414)
[Pool.sol#L523](https://github.com/code-423n4/2022-12-Stealth-Project/blob/fc8589d7d8c1d8488fd97ccc46e1ff11c8426ac2/maverick-v1/contracts/models/Pool.sol#L523)
[Pool.sol#L540](https://github.com/code-423n4/2022-12-Stealth-Project/blob/fc8589d7d8c1d8488fd97ccc46e1ff11c8426ac2/maverick-v1/contracts/models/Pool.sol#L540)
[Pool.sol#L653](https://github.com/code-423n4/2022-12-Stealth-Project/blob/fc8589d7d8c1d8488fd97ccc46e1ff11c8426ac2/maverick-v1/contracts/models/Pool.sol#L653)
[PoolInspector.sol#L30](https://github.com/code-423n4/2022-12-Stealth-Project/blob/fc8589d7d8c1d8488fd97ccc46e1ff11c8426ac2/maverick-v1/contracts/models/PoolInspector.sol#L30)
[PoolInspector.sol#L80](https://github.com/code-423n4/2022-12-Stealth-Project/blob/fc8589d7d8c1d8488fd97ccc46e1ff11c8426ac2/maverick-v1/contracts/models/PoolInspector.sol#L80)
[PoolInspector.sol#L101](https://github.com/code-423n4/2022-12-Stealth-Project/blob/fc8589d7d8c1d8488fd97ccc46e1ff11c8426ac2/maverick-v1/contracts/models/PoolInspector.sol#L101)
