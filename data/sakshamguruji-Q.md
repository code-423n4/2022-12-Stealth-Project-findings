## setOwner FUNCTION SHOULD BE BROKEN DOWN INTO 2 FUNCTIONS

### Description:

Functions which set owners/privileged roles and can be updated by that same role only should be done in two steps , 
ex - A setOwner function which sets the new owner and can be only be called by the current owner , if by mistake
the current owner sets the wrong address as the new owner then the wrong address is the current owner and it 
can never be changed again.

Make this function into a two step process https://github.com/code-423n4/2022-12-Stealth-Project/blob/main/maverick-v1/contracts/models/Factory.sol#L40 
where we first assign a temp new owner then that temp address has to accept the new ownership.

## LACK OF COMMENTS THROUGHOUT THE CODEBASE

### Description:

There should be detailed comments about state variables and function to make the codebase easier to understand and readable.

Affected instances:

https://github.com/code-423n4/2022-12-Stealth-Project/blob/main/maverick-v1/contracts/models/Pool.sol#L31-L60
All the functions in Pool.sol

## REQUIRE STATEMENTS SHOULD HAVE A REVERT STRING TO BETTER UNDERSTAND THE ERROR

### Description:

Require statements should always have a revert string to let the user better understand the error 
that occured.

Affected instances:
https://github.com/code-423n4/2022-12-Stealth-Project/blob/main/maverick-v1/contracts/models/Pool.sol#L129
https://github.com/code-423n4/2022-12-Stealth-Project/blob/main/maverick-v1/contracts/models/Pool.sol#L342
https://github.com/code-423n4/2022-12-Stealth-Project/blob/main/maverick-v1/contracts/models/Pool.sol#L333
https://github.com/code-423n4/2022-12-Stealth-Project/blob/main/router-v1/contracts/Router.sol#L106
https://github.com/code-423n4/2022-12-Stealth-Project/blob/main/router-v1/contracts/Router.sol#L206
https://github.com/code-423n4/2022-12-Stealth-Project/blob/main/router-v1/contracts/Router.sol#L205


## LACK OF ZERO ADDRESS CHECKS

### Description:

By mistake any of these could be address(0) the could be chaged later by and admin, however is a good practice to check for address(0).
Sometimes setting a parameter to 0-address might result in sending funds to the 0 address which results in permanent loss of funds.

Affected instances:
https://github.com/code-423n4/2022-12-Stealth-Project/blob/main/maverick-v1/contracts/models/Pool.sol#L209
https://github.com/code-423n4/2022-12-Stealth-Project/blob/main/maverick-v1/contracts/models/Pool.sol#L260
https://github.com/code-423n4/2022-12-Stealth-Project/blob/main/maverick-v1/contracts/models/Pool.sol#L320
https://github.com/code-423n4/2022-12-Stealth-Project/blob/main/maverick-v1/contracts/models/Pool.sol#L339
https://github.com/code-423n4/2022-12-Stealth-Project/blob/main/maverick-v1/contracts/models/Position.sol#L28
https://github.com/code-423n4/2022-12-Stealth-Project/blob/main/maverick-v1/contracts/models/Position.sol#L34
https://github.com/code-423n4/2022-12-Stealth-Project/blob/main/router-v1/contracts/Router.sol#L48
https://github.com/code-423n4/2022-12-Stealth-Project/blob/main/router-v1/contracts/Router.sol#L59
https://github.com/code-423n4/2022-12-Stealth-Project/blob/main/router-v1/contracts/Router.sol#L70
https://github.com/code-423n4/2022-12-Stealth-Project/blob/main/router-v1/contracts/Router.sol#L88
https://github.com/code-423n4/2022-12-Stealth-Project/blob/main/router-v1/contracts/libraries/SelfPermit.sol#L16
https://github.com/code-423n4/2022-12-Stealth-Project/blob/main/router-v1/contracts/libraries/SelfPermit.sol#L26
https://github.com/code-423n4/2022-12-Stealth-Project/blob/main/router-v1/contracts/libraries/SelfPermit.sol#L21
https://github.com/code-423n4/2022-12-Stealth-Project/blob/main/router-v1/contracts/libraries/SelfPermit.sol#L31
https://github.com/code-423n4/2022-12-Stealth-Project/blob/main/maverick-v1/contracts/libraries/SafeERC20Min.sol#L11
https://github.com/code-423n4/2022-12-Stealth-Project/blob/main/router-v1/contracts/libraries/TransferHelper.sol#L13
https://github.com/code-423n4/2022-12-Stealth-Project/blob/main/router-v1/contracts/libraries/TransferHelper.sol#L23
https://github.com/code-423n4/2022-12-Stealth-Project/blob/main/router-v1/contracts/libraries/TransferHelper.sol#L33
https://github.com/code-423n4/2022-12-Stealth-Project/blob/main/router-v1/contracts/libraries/TransferHelper.sol#L42





## MISSING CHECK FOR EMPTY STRING

### Description:

The function here https://github.com/code-423n4/2022-12-Stealth-Project/blob/main/maverick-v1/contracts/models/PositionMetadata.sol#L17 
lacks the check to check if the uri string is an empty string or not . The value can be changed to the correct value by calling
the fucntion again witht the correct value but it is a good practice to have this check.

## INTERNAL FUNCTIONS SHOULD HAVE A UNDERSCORE AT THE STARTING OF THE FUNCTION NAME

### Description:

According to the naming convention , internal function name should start with a underscore.

Affected instances:
https://github.com/code-423n4/2022-12-Stealth-Project/blob/main/maverick-v1/contracts/models/Pool.sol#L86
https://github.com/code-423n4/2022-12-Stealth-Project/blob/main/maverick-v1/contracts/models/Pool.sol#L90
https://github.com/code-423n4/2022-12-Stealth-Project/blob/main/maverick-v1/contracts/models/Pool.sol#L320
https://github.com/code-423n4/2022-12-Stealth-Project/blob/main/maverick-v1/contracts/models/Pool.sol#L332
https://github.com/code-423n4/2022-12-Stealth-Project/blob/main/router-v1/contracts/Router.sol#L88
https://github.com/code-423n4/2022-12-Stealth-Project/blob/main/router-v1/contracts/Router.sol#L121
https://github.com/code-423n4/2022-12-Stealth-Project/blob/main/router-v1/contracts/libraries/Path.sol#L27
https://github.com/code-423n4/2022-12-Stealth-Project/blob/main/router-v1/contracts/libraries/Path.sol#L34
https://github.com/code-423n4/2022-12-Stealth-Project/blob/main/router-v1/contracts/libraries/Path.sol#L44
https://github.com/code-423n4/2022-12-Stealth-Project/blob/main/router-v1/contracts/libraries/Path.sol#L53
https://github.com/code-423n4/2022-12-Stealth-Project/blob/main/router-v1/contracts/libraries/Path.sol#L60
https://github.com/code-423n4/2022-12-Stealth-Project/blob/main/router-v1/contracts/libraries/BytesLib.sol#L12
https://github.com/code-423n4/2022-12-Stealth-Project/blob/main/router-v1/contracts/libraries/BytesLib.sol#L74
https://github.com/code-423n4/2022-12-Stealth-Project/blob/main/router-v1/contracts/libraries/BytesLib.sol#L86
All functions in Bin.sol
All functions in BinMap.sol
All functions in BinMath.sol
All functions in Cast.sol
All functions in Delta.sol
All functions in Math.sol
All functions in SafeERC20Min.sol
All functions in Twa.sol
All functions in TransferHelper.sol