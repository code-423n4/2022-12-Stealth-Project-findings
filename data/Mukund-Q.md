## INDEX

| NO. | Issue                                                         |
|-----|---------------------------------------------------------------|
| 1   | Missing address(0) check                                      |
| 2   | `require()` statements should have descriptive reason strings |
| 3   | File is missing NatSpec                                       |
| 4   | Event is missing indexed fields                               |
| 5   | Contracts are not using their OZ Upgradeable counterparts     |
| 6   | Critical Changes Should Use Two-step Procedure                |
| 7   | Missing parameter validation                                  |
| 8   | Usage of transfer can lead to loss of funds                   |
| 9   | Implementation contract may not be initialized                |
|10  | USE NAMED IMPORTS INSTEAD OF PLAIN `IMPORT ‘FILE.SOL’ |

## 1. Missing address(0) check 
[Pool.sol#L320-L330](https://github.com/code-423n4/2022-12-Stealth-Project/blob/main/maverick-v1/contracts/models/Pool.sol#L320-L330)
[Pool.sol#L339-L350](https://github.com/code-423n4/2022-12-Stealth-Project/blob/main/maverick-v1/contracts/models/Pool.sol#L339-L350)

## 2. `require()` statements should have descriptive reason strings
there is no reason in require statement why it get reverted
[Pool.sol#L333](https://github.com/code-423n4/2022-12-Stealth-Project/blob/main/maverick-v1/contracts/models/Pool.sol#L333)
[Pool.sol#L342](https://github.com/code-423n4/2022-12-Stealth-Project/blob/main/maverick-v1/contracts/models/Pool.sol#L342)
[Router.sol#L106](https://github.com/code-423n4/2022-12-Stealth-Project/blob/main/router-v1/contracts/Router.sol#L106)
[Router.sol#L205-L206](https://github.com/code-423n4/2022-12-Stealth-Project/blob/main/router-v1/contracts/Router.sol#L205-L206)

## 3. File is missing NatSpec
consider adding NatSpec for better understanding of contract
[Factory.sol](https://github.com/code-423n4/2022-12-Stealth-Project/blob/main/maverick-v1/contracts/models/Factory.sol)
[Pool.sol](https://github.com/code-423n4/2022-12-Stealth-Project/blob/main/maverick-v1/contracts/models/Pool.sol)
[PoolInspector.sol](https://github.com/code-423n4/2022-12-Stealth-Project/blob/main/maverick-v1/contracts/models/PoolInspector.sol)
[Position.sol](https://github.com/code-423n4/2022-12-Stealth-Project/blob/main/maverick-v1/contracts/models/Position.sol)

## 4. Event is missing indexed fields
Index event fields make the field more quickly accessible to off-chain tools that parse events. However, note that each index field costs extra gas during emission, so it's not necessarily best to index the maximum allowed per event (three fields). Each event should use three indexed fields if there are three or more fields, and gas usage is not particularly of concern for the events in question. If there are fewer than three fields, all of the fields should be indexed.
[IPool.sol#L8-L20](https://github.com/code-423n4/2022-12-Stealth-Project/blob/main/maverick-v1/contracts/interfaces/IPool.sol#L8-L20)

## 5. Contracts are not using their OZ Upgradeable counterparts
The non-upgradeable standard version of OpenZeppelin’s library are inherited / used by the contracts.
It would be safer to use the upgradeable versions of the library contracts to avoid unexpected behaviour.
[Factory.sol#L4-L5](https://github.com/code-423n4/2022-12-Stealth-Project/blob/main/maverick-v1/contracts/models/Factory.sol#L4-L5)
[Pool.sol#L4](https://github.com/code-423n4/2022-12-Stealth-Project/blob/main/maverick-v1/contracts/models/Pool.sol#L4)
[Position.sol#L4-L7](https://github.com/code-423n4/2022-12-Stealth-Project/blob/main/maverick-v1/contracts/models/Position.sol#L4-L7)
[PositionMetadata.sol#L5-L6](https://github.com/code-423n4/2022-12-Stealth-Project/blob/main/maverick-v1/contracts/models/PositionMetadata.sol#L5-L6)

## 6. Critical Changes Should Use Two-step Procedure
The critical procedures should be two step process.
[Factory.sol#L33-L38](https://github.com/code-423n4/2022-12-Stealth-Project/blob/main/maverick-v1/contracts/models/Factory.sol#L33-L38)
[Factory.sol#L40-L44](https://github.com/code-423n4/2022-12-Stealth-Project/blob/main/maverick-v1/contracts/models/Factory.sol#L40-L44)
[Pool.sol#L332-L337](https://github.com/code-423n4/2022-12-Stealth-Project/blob/main/maverick-v1/contracts/models/Pool.sol#L332-L337)
[Position.sol#L23-L26](https://github.com/code-423n4/2022-12-Stealth-Project/blob/main/maverick-v1/contracts/models/Position.sol#L23-L26)

## 7. Missing parameter validation
some value in constructors are not checked for invalid values
[Pool.sol#L60-L84](https://github.com/code-423n4/2022-12-Stealth-Project/blob/main/maverick-v1/contracts/models/Pool.sol#L60-L84)

## 8. Usage of transfer can lead to loss of funds
The funds that are to be sent can be lost. The issues with transfer() are outlined here:
https://consensys.net/diligence/blog/2019/09/stop-using-soliditys-transfer-now/
[Router.sol#L91](https://github.com/code-423n4/2022-12-Stealth-Project/blob/main/router-v1/contracts/Router.sol#L91)

## 9. Implementation contract may not be initialized
OpenZeppelin recommends that the initializer modifier be applied to constructors.
Per OZs Post implementation contract should be initialized to avoid potential griefs or exploits.
https://forum.openzeppelin.com/t/uupsupgradeable-vulnerability-post-mortem/15680/5
[Pool.sol#L60-L84](https://github.com/code-423n4/2022-12-Stealth-Project/blob/main/maverick-v1/contracts/models/Pool.sol#L60-L84)

## 10.USE NAMED IMPORTS INSTEAD OF PLAIN `IMPORT ‘FILE.SOL’
for example use it like import `{Ownable} from "@openzeppelin/contracts/access/Ownable.sol";`
[Router.sol#L4-L16](https://github.com/code-423n4/2022-12-Stealth-Project/blob/main/router-v1/contracts/Router.sol#L4-L16)
[Factory.sol#L4-L12](https://github.com/code-423n4/2022-12-Stealth-Project/blob/main/maverick-v1/contracts/models/Factory.sol#L4-L12)
[Pool.sol#L4-L20](https://github.com/code-423n4/2022-12-Stealth-Project/blob/main/maverick-v1/contracts/models/Pool.sol#L4-L20)
[PoolInspector.sol#L4-L9](https://github.com/code-423n4/2022-12-Stealth-Project/blob/main/maverick-v1/contracts/models/PoolInspector.sol#L4-L9)
[Position.sol#L4-L10](https://github.com/code-423n4/2022-12-Stealth-Project/blob/main/maverick-v1/contracts/models/Position.sol#L4-L10)