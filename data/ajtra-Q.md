# NC01. Integer overflow due to unsafe casting
## Description
In solidity, even in versions after 0.8, it does not control overflows in casting only in mathematical operations. 

## Mitigation
Use OpenZeppeling [SafeCast](https://docs.openzeppelin.com/contracts/3.x/api/utils#SafeCast)

## Lines in the code
[Pool.sol#L294](https://github.com/code-423n4/2022-12-Stealth-Project/blob/fc8589d7d8c1d8488fd97ccc46e1ff11c8426ac2/maverick-v1/contracts/models/Pool.sol#L294)
[Pool.sol#L550](https://github.com/code-423n4/2022-12-Stealth-Project/blob/fc8589d7d8c1d8488fd97ccc46e1ff11c8426ac2/maverick-v1/contracts/models/Pool.sol#L550)

# NC02. Struct definition should be before state variable declarations
## Lines in the code
[Pool.sol#L105-L111](https://github.com/code-423n4/2022-12-Stealth-Project/blob/fc8589d7d8c1d8488fd97ccc46e1ff11c8426ac2/maverick-v1/contracts/models/Pool.sol#L105-L111)

# NC03. Lack of functions NatSpec
## Description
It is important to include NatSpec in every function for a correct interpretation of the code. 
In the following [link](https://docs.soliditylang.org/en/v0.8.17/natspec-format.html) you can find the documentation.

# NC04. File is missing NatSpec
## Lines in the code
[Router.sol](https://github.com/code-423n4/2022-12-Stealth-Project/blob/fc8589d7d8c1d8488fd97ccc46e1ff11c8426ac2/router-v1/contracts/Router.sol)
[IMulticall.sol](https://github.com/code-423n4/2022-12-Stealth-Project/blob/fc8589d7d8c1d8488fd97ccc46e1ff11c8426ac2/router-v1/contracts/interfaces/IMulticall.sol)
[IMulticall.sol](https://github.com/code-423n4/2022-12-Stealth-Project/blob/fc8589d7d8c1d8488fd97ccc46e1ff11c8426ac2/router-v1/contracts/interfaces/IMulticall.sol)
[IRouter.sol](https://github.com/code-423n4/2022-12-Stealth-Project/blob/fc8589d7d8c1d8488fd97ccc46e1ff11c8426ac2/router-v1/contracts/interfaces/IRouter.sol)
[ISelfPermit.sol](https://github.com/code-423n4/2022-12-Stealth-Project/blob/fc8589d7d8c1d8488fd97ccc46e1ff11c8426ac2/router-v1/contracts/interfaces/ISelfPermit.sol)
[BytesLib.sol](https://github.com/code-423n4/2022-12-Stealth-Project/blob/fc8589d7d8c1d8488fd97ccc46e1ff11c8426ac2/router-v1/contracts/libraries/BytesLib.sol)
[Deadline.sol](https://github.com/code-423n4/2022-12-Stealth-Project/blob/fc8589d7d8c1d8488fd97ccc46e1ff11c8426ac2/router-v1/contracts/libraries/Deadline.sol)
[Multicall.sol](https://github.com/code-423n4/2022-12-Stealth-Project/blob/fc8589d7d8c1d8488fd97ccc46e1ff11c8426ac2/router-v1/contracts/libraries/Multicall.sol)
[Multicall.sol](https://github.com/code-423n4/2022-12-Stealth-Project/blob/fc8589d7d8c1d8488fd97ccc46e1ff11c8426ac2/router-v1/contracts/libraries/Multicall.sol)
[Path.sol](https://github.com/code-423n4/2022-12-Stealth-Project/blob/fc8589d7d8c1d8488fd97ccc46e1ff11c8426ac2/router-v1/contracts/libraries/Path.sol)
[SelfPermit.sol](https://github.com/code-423n4/2022-12-Stealth-Project/blob/fc8589d7d8c1d8488fd97ccc46e1ff11c8426ac2/router-v1/contracts/libraries/SelfPermit.sol)
[TransferHelper.sol](https://github.com/code-423n4/2022-12-Stealth-Project/blob/fc8589d7d8c1d8488fd97ccc46e1ff11c8426ac2/router-v1/contracts/libraries/TransferHelper.sol)
[IERC20PermitAllowed.sol](https://github.com/code-423n4/2022-12-Stealth-Project/blob/fc8589d7d8c1d8488fd97ccc46e1ff11c8426ac2/router-v1/contracts/interfaces/external/IERC20PermitAllowed.sol)
[IWETH9.sol](https://github.com/code-423n4/2022-12-Stealth-Project/blob/fc8589d7d8c1d8488fd97ccc46e1ff11c8426ac2/router-v1/contracts/interfaces/external/IWETH9.sol)
[IAddLiquidityCallback.sol](https://github.com/code-423n4/2022-12-Stealth-Project/blob/fc8589d7d8c1d8488fd97ccc46e1ff11c8426ac2/maverick-v1/contracts/interfaces/IAddLiquidityCallback.sol)
[IFactory.sol](https://github.com/code-423n4/2022-12-Stealth-Project/blob/fc8589d7d8c1d8488fd97ccc46e1ff11c8426ac2/maverick-v1/contracts/interfaces/IFactory.sol)
[IPool.sol](https://github.com/code-423n4/2022-12-Stealth-Project/blob/fc8589d7d8c1d8488fd97ccc46e1ff11c8426ac2/maverick-v1/contracts/interfaces/IPool.sol)
[IPoolAdmin.sol](https://github.com/code-423n4/2022-12-Stealth-Project/blob/fc8589d7d8c1d8488fd97ccc46e1ff11c8426ac2/maverick-v1/contracts/interfaces/IPoolAdmin.sol)
[IPositionMetadata.sol](https://github.com/code-423n4/2022-12-Stealth-Project/blob/fc8589d7d8c1d8488fd97ccc46e1ff11c8426ac2/maverick-v1/contracts/interfaces/IPositionMetadata.sol)
[IPosition.sol](https://github.com/code-423n4/2022-12-Stealth-Project/blob/fc8589d7d8c1d8488fd97ccc46e1ff11c8426ac2/maverick-v1/contracts/interfaces/IPosition.sol)
[ISwapCallback.sol](https://github.com/code-423n4/2022-12-Stealth-Project/blob/fc8589d7d8c1d8488fd97ccc46e1ff11c8426ac2/maverick-v1/contracts/interfaces/ISwapCallback.sol)
[Bin.sol](https://github.com/code-423n4/2022-12-Stealth-Project/blob/fc8589d7d8c1d8488fd97ccc46e1ff11c8426ac2/maverick-v1/contracts/libraries/Bin.sol)
[BinMap.sol](https://github.com/code-423n4/2022-12-Stealth-Project/blob/fc8589d7d8c1d8488fd97ccc46e1ff11c8426ac2/maverick-v1/contracts/libraries/BinMap.sol)
[BinMath.sol](https://github.com/code-423n4/2022-12-Stealth-Project/blob/fc8589d7d8c1d8488fd97ccc46e1ff11c8426ac2/maverick-v1/contracts/libraries/BinMath.sol)
[Cast.sol](https://github.com/code-423n4/2022-12-Stealth-Project/blob/fc8589d7d8c1d8488fd97ccc46e1ff11c8426ac2/maverick-v1/contracts/libraries/Cast.sol)
[Constants.sol](https://github.com/code-423n4/2022-12-Stealth-Project/blob/fc8589d7d8c1d8488fd97ccc46e1ff11c8426ac2/maverick-v1/contracts/libraries/Constants.sol)
[Delta.sol](https://github.com/code-423n4/2022-12-Stealth-Project/blob/fc8589d7d8c1d8488fd97ccc46e1ff11c8426ac2/maverick-v1/contracts/libraries/Delta.sol)
[Deployer.sol](https://github.com/code-423n4/2022-12-Stealth-Project/blob/fc8589d7d8c1d8488fd97ccc46e1ff11c8426ac2/maverick-v1/contracts/libraries/Deployer.sol)
[Math.sol](https://github.com/code-423n4/2022-12-Stealth-Project/blob/fc8589d7d8c1d8488fd97ccc46e1ff11c8426ac2/maverick-v1/contracts/libraries/Math.sol)
[SafeERC20Min.sol](https://github.com/code-423n4/2022-12-Stealth-Project/blob/fc8589d7d8c1d8488fd97ccc46e1ff11c8426ac2/maverick-v1/contracts/libraries/SafeERC20Min.sol)
[Twa.sol](https://github.com/code-423n4/2022-12-Stealth-Project/blob/fc8589d7d8c1d8488fd97ccc46e1ff11c8426ac2/maverick-v1/contracts/libraries/Twa.sol)
[Factory.sol](https://github.com/code-423n4/2022-12-Stealth-Project/blob/fc8589d7d8c1d8488fd97ccc46e1ff11c8426ac2/maverick-v1/contracts/models/Factory.sol)
[Pool.sol](https://github.com/code-423n4/2022-12-Stealth-Project/blob/fc8589d7d8c1d8488fd97ccc46e1ff11c8426ac2/maverick-v1/contracts/models/Pool.sol)
[PoolInspector.sol](https://github.com/code-423n4/2022-12-Stealth-Project/blob/fc8589d7d8c1d8488fd97ccc46e1ff11c8426ac2/maverick-v1/contracts/models/PoolInspector.sol)
[Position.sol](https://github.com/code-423n4/2022-12-Stealth-Project/blob/fc8589d7d8c1d8488fd97ccc46e1ff11c8426ac2/maverick-v1/contracts/models/Position.sol)
[PositionMetadata.sol](https://github.com/code-423n4/2022-12-Stealth-Project/blob/fc8589d7d8c1d8488fd97ccc46e1ff11c8426ac2/maverick-v1/contracts/models/PositionMetadata.sol)


# NC05 - Lines are too long
## Description
Usually lines in source code are limited to 120 characters (version 0.8.17). 
Today's screens are much larger so it's reasonable to stretch this in some cases. 
Solidity reference in the following [link](https://docs.soliditylang.org/en/v0.8.17/style-guide.html#maximum-line-length)

## Lines in the code
[Factory.sol#L46](https://github.com/code-423n4/2022-12-Stealth-Project/blob/fc8589d7d8c1d8488fd97ccc46e1ff11c8426ac2/maverick-v1/contracts/models/Factory.sol#L46)
[Factory.sol#L58](https://github.com/code-423n4/2022-12-Stealth-Project/blob/fc8589d7d8c1d8488fd97ccc46e1ff11c8426ac2/maverick-v1/contracts/models/Factory.sol#L58)
[Router.sol#L121](https://github.com/code-423n4/2022-12-Stealth-Project/blob/fc8589d7d8c1d8488fd97ccc46e1ff11c8426ac2/router-v1/contracts/Router.sol#L121)
[Router.sol#L132](https://github.com/code-423n4/2022-12-Stealth-Project/blob/fc8589d7d8c1d8488fd97ccc46e1ff11c8426ac2/router-v1/contracts/Router.sol#L132)
[Router.sol#L137](https://github.com/code-423n4/2022-12-Stealth-Project/blob/fc8589d7d8c1d8488fd97ccc46e1ff11c8426ac2/router-v1/contracts/Router.sol#L137)
[Router.sol#L143](https://github.com/code-423n4/2022-12-Stealth-Project/blob/fc8589d7d8c1d8488fd97ccc46e1ff11c8426ac2/router-v1/contracts/Router.sol#L143)
[Router.sol#L181](https://github.com/code-423n4/2022-12-Stealth-Project/blob/fc8589d7d8c1d8488fd97ccc46e1ff11c8426ac2/router-v1/contracts/Router.sol#L181)
[Router.sol#L185](https://github.com/code-423n4/2022-12-Stealth-Project/blob/fc8589d7d8c1d8488fd97ccc46e1ff11c8426ac2/router-v1/contracts/Router.sol#L185)
[Router.sol#L193](https://github.com/code-423n4/2022-12-Stealth-Project/blob/fc8589d7d8c1d8488fd97ccc46e1ff11c8426ac2/router-v1/contracts/Router.sol#L193)
[Router.sol#L194](https://github.com/code-423n4/2022-12-Stealth-Project/blob/fc8589d7d8c1d8488fd97ccc46e1ff11c8426ac2/router-v1/contracts/Router.sol#L194)
[Router.sol#L231](https://github.com/code-423n4/2022-12-Stealth-Project/blob/fc8589d7d8c1d8488fd97ccc46e1ff11c8426ac2/router-v1/contracts/Router.sol#L231)
[Router.sol#L245](https://github.com/code-423n4/2022-12-Stealth-Project/blob/fc8589d7d8c1d8488fd97ccc46e1ff11c8426ac2/router-v1/contracts/Router.sol#L245)
[Router.sol#L259](https://github.com/code-423n4/2022-12-Stealth-Project/blob/fc8589d7d8c1d8488fd97ccc46e1ff11c8426ac2/router-v1/contracts/Router.sol#L259)
[Router.sol#L269](https://github.com/code-423n4/2022-12-Stealth-Project/blob/fc8589d7d8c1d8488fd97ccc46e1ff11c8426ac2/router-v1/contracts/Router.sol#L269)
[Router.sol#L272](https://github.com/code-423n4/2022-12-Stealth-Project/blob/fc8589d7d8c1d8488fd97ccc46e1ff11c8426ac2/router-v1/contracts/Router.sol#L272)
[Router.sol#L284](https://github.com/code-423n4/2022-12-Stealth-Project/blob/fc8589d7d8c1d8488fd97ccc46e1ff11c8426ac2/router-v1/contracts/Router.sol#L284)
[Router.sol#L290](https://github.com/code-423n4/2022-12-Stealth-Project/blob/fc8589d7d8c1d8488fd97ccc46e1ff11c8426ac2/router-v1/contracts/Router.sol#L290)
[PoolInspector.sol#L33](https://github.com/code-423n4/2022-12-Stealth-Project/blob/fc8589d7d8c1d8488fd97ccc46e1ff11c8426ac2/maverick-v1/contracts/models/PoolInspector.sol#L33)
[PoolInspector.sol#L95](https://github.com/code-423n4/2022-12-Stealth-Project/blob/fc8589d7d8c1d8488fd97ccc46e1ff11c8426ac2/maverick-v1/contracts/models/PoolInspector.sol#L95)
[PoolInspector.sol#L107](https://github.com/code-423n4/2022-12-Stealth-Project/blob/fc8589d7d8c1d8488fd97ccc46e1ff11c8426ac2/maverick-v1/contracts/models/PoolInspector.sol#L107)
[Pool.sol#L534](https://github.com/code-423n4/2022-12-Stealth-Project/blob/fc8589d7d8c1d8488fd97ccc46e1ff11c8426ac2/maverick-v1/contracts/models/Pool.sol#L534)
[Pool.sol#L550](https://github.com/code-423n4/2022-12-Stealth-Project/blob/fc8589d7d8c1d8488fd97ccc46e1ff11c8426ac2/maverick-v1/contracts/models/Pool.sol#L550)
[Pool.sol#L565](https://github.com/code-423n4/2022-12-Stealth-Project/blob/fc8589d7d8c1d8488fd97ccc46e1ff11c8426ac2/maverick-v1/contracts/models/Pool.sol#L565)
[Pool.sol#L589](https://github.com/code-423n4/2022-12-Stealth-Project/blob/fc8589d7d8c1d8488fd97ccc46e1ff11c8426ac2/maverick-v1/contracts/models/Pool.sol#L589)
[Pool.sol#L602](https://github.com/code-423n4/2022-12-Stealth-Project/blob/fc8589d7d8c1d8488fd97ccc46e1ff11c8426ac2/maverick-v1/contracts/models/Pool.sol#L602)
[Pool.sol#L636](https://github.com/code-423n4/2022-12-Stealth-Project/blob/fc8589d7d8c1d8488fd97ccc46e1ff11c8426ac2/maverick-v1/contracts/models/Pool.sol#L636)
[Pool.sol#L639](https://github.com/code-423n4/2022-12-Stealth-Project/blob/fc8589d7d8c1d8488fd97ccc46e1ff11c8426ac2/maverick-v1/contracts/models/Pool.sol#L639)


# NC06 - Unify require in a modifier
## Description
Create a modifier when a require is used several times.

## Factory:PROTOCOL_FEE_CANNOT_EXCEED_ONE
[Factory.sol#L27](https://github.com/code-423n4/2022-12-Stealth-Project/blob/fc8589d7d8c1d8488fd97ccc46e1ff11c8426ac2/maverick-v1/contracts/models/Factory.sol#L27)
[Factory.sol#L35](https://github.com/code-423n4/2022-12-Stealth-Project/blob/fc8589d7d8c1d8488fd97ccc46e1ff11c8426ac2/maverick-v1/contracts/models/Factory.sol#L35)
