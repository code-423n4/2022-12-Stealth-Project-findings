### Typos

Consider fixing these minor spelling mistakes before deployment

https://github.com/code-423n4/2022-12-Stealth-Project/blob/fc8589d7d8c1d8488fd97ccc46e1ff11c8426ac2/maverick-v1/contracts/interfaces/IPool.sol#L167
IPool.sol:167:    //mergeId is the currrent active bin.
should be 
IPool.sol:167:    //mergeId is the current active bin.

https://github.com/code-423n4/2022-12-Stealth-Project/blob/fc8589d7d8c1d8488fd97ccc46e1ff11c8426ac2/maverick-v1/contracts/interfaces/IPool.sol#L63

IPool.sol:63:    /// @param mergeBinBalance LP token balance that this bin posseses of the merge bin
should be 
IPool.sol:63:    /// @param mergeBinBalance LP token balance that this bin posseses of the merge bin

https://github.com/code-423n4/2022-12-Stealth-Project/blob/fc8589d7d8c1d8488fd97ccc46e1ff11c8426ac2/maverick-v1/contracts/interfaces/IPool.sol#L26
IPool.sol:26:    /// @notice return paramters for Add/Remove liquidity
should be 
IPool.sol:26:    /// @notice return parameters for Add/Remove liquidity

https://github.com/code-423n4/2022-12-Stealth-Project/blob/fc8589d7d8c1d8488fd97ccc46e1ff11c8426ac2/maverick-v1/contracts/interfaces/IPoolAdmin.sol#L6
IPoolAdmin.sol:6:    /// @param action set emergnecy mode (911), set protocol fees(1), claim A
should be 
IPoolAdmin.sol:6:    /// @param action set emergenecy mode (911), set protocol fees(1), claim A

https://github.com/code-423n4/2022-12-Stealth-Project/blob/fc8589d7d8c1d8488fd97ccc46e1ff11c8426ac2/router-v1/contracts/interfaces/IRouter.sol#L177
IRouter.sol:177:    /// @param params paramters of liquidity removal
should be
IRouter.sol:177:    /// @param params parameters of liquidity removal

https://github.com/code-423n4/2022-12-Stealth-Project/blob/fc8589d7d8c1d8488fd97ccc46e1ff11c8426ac2/router-v1/contracts/interfaces/IRouter.sol#L132
IRouter.sol:132:    /// @param params paramters of liquidity addition
should be 
IRouter.sol:132:    /// @param params parameters of liquidity addition

https://github.com/code-423n4/2022-12-Stealth-Project/blob/fc8589d7d8c1d8488fd97ccc46e1ff11c8426ac2/router-v1/contracts/interfaces/IRouter.sol#L116
IRouter.sol:116:    /// @param addParams paramters of liquidity addition
should be
IRouter.sol:116:    /// @param addParams parameters of liquidity addition

### Variable naming conventions

Variable names that consist of all capital letters should be reserved for constant/immutable variables

https://github.com/code-423n4/2022-12-Stealth-Project/blob/fc8589d7d8c1d8488fd97ccc46e1ff11c8426ac2/maverick-v1/contracts/models/Pool.sol#L42 - #l49

https://github.com/code-423n4/2022-12-Stealth-Project/blob/fc8589d7d8c1d8488fd97ccc46e1ff11c8426ac2/maverick-v1/contracts/models/Factory.sol#L23

### Incomplete/Missing natspec comments

The project is full of contracts/files with incomplete natspec or no natspec which makes and made understanding and auditing the project that much more time consuming and difficult, consider leaving clearer natspec comments to make the contracts of the project easier to understand from an ousiders point of view, such as anyone who wants to interact with the project, currently information is very limited unless you search for porject papers online.

ALL files under scope suffer with the above issue

### Require/Revert and descriptive strings

require and revert should have descriptive reasons, or at least a more descriptive reason than comments such as just "A" as found in pool.sol line 168

https://github.com/code-423n4/2022-12-Stealth-Project/blob/fc8589d7d8c1d8488fd97ccc46e1ff11c8426ac2/maverick-v1/contracts/models/Pool.sol#L129

https://github.com/code-423n4/2022-12-Stealth-Project/blob/fc8589d7d8c1d8488fd97ccc46e1ff11c8426ac2/maverick-v1/contracts/models/Pool.sol#L168

https://github.com/code-423n4/2022-12-Stealth-Project/blob/fc8589d7d8c1d8488fd97ccc46e1ff11c8426ac2/maverick-v1/contracts/models/Pool.sol#L303

https://github.com/code-423n4/2022-12-Stealth-Project/blob/fc8589d7d8c1d8488fd97ccc46e1ff11c8426ac2/maverick-v1/contracts/models/PoolInspector.sol#L58

https://github.com/code-423n4/2022-12-Stealth-Project/blob/fc8589d7d8c1d8488fd97ccc46e1ff11c8426ac2/maverick-v1/contracts/models/PoolInspector.sol#L60

https://github.com/code-423n4/2022-12-Stealth-Project/blob/fc8589d7d8c1d8488fd97ccc46e1ff11c8426ac2/maverick-v1/contracts/libraries/BinMath.sol#L94

https://github.com/code-423n4/2022-12-Stealth-Project/blob/fc8589d7d8c1d8488fd97ccc46e1ff11c8426ac2/maverick-v1/contracts/libraries/Bin.sol#L85
https://github.com/code-423n4/2022-12-Stealth-Project/blob/fc8589d7d8c1d8488fd97ccc46e1ff11c8426ac2/maverick-v1/contracts/libraries/Bin.sol#L140

https://github.com/code-423n4/2022-12-Stealth-Project/blob/fc8589d7d8c1d8488fd97ccc46e1ff11c8426ac2/maverick-v1/contracts/libraries/Cast.sol#L6

https://github.com/code-423n4/2022-12-Stealth-Project/blob/fc8589d7d8c1d8488fd97ccc46e1ff11c8426ac2/maverick-v1/contracts/libraries/SafeERC20Min.sol#L18

https://github.com/code-423n4/2022-12-Stealth-Project/blob/fc8589d7d8c1d8488fd97ccc46e1ff11c8426ac2/router-v1/contracts/libraries/Mulitcall.sol#L18

https://github.com/code-423n4/2022-12-Stealth-Project/blob/fc8589d7d8c1d8488fd97ccc46e1ff11c8426ac2/router-v1/contracts/libraries/Mulitcall.sol#L22

### Inconsitant use of solidity version and outdated version

Use a later version of solidity then 0.8.0 or 0.8.4 and use a consitant version accross all of the contracts

Currently the project uses version .0.8.0 and 0.8.4 inconsitantly accross its contract, consider aligning the versions used and using a more recent version of solidity to take advantage of any upgrades that have been made to the code since .0.8.0 current version avaliable is 0.8.17

Found in all files under scope

### Public functions to external

public functions not called by the contract should be declared external instead
Contracts are allowed to override their parents' functions and change the visibility from external to public.

https://github.com/code-423n4/2022-12-Stealth-Project/blob/fc8589d7d8c1d8488fd97ccc46e1ff11c8426ac2/router-v1/contracts/libraries/Multicall.sol#L11

https://github.com/code-423n4/2022-12-Stealth-Project/blob/fc8589d7d8c1d8488fd97ccc46e1ff11c8426ac2/router-v1/contracts/libraries/SelfPermit.sol#L16

https://github.com/code-423n4/2022-12-Stealth-Project/blob/fc8589d7d8c1d8488fd97ccc46e1ff11c8426ac2/router-v1/contracts/libraries/Selfpermit.sol#L26

## Function does not handle if both values are 0

unsure of severity for this so added to QA as appose to having issue invalidated 

https://github.com/code-423n4/2022-12-Stealth-Project/blob/fc8589d7d8c1d8488fd97ccc46e1ff11c8426ac2/maverick-v1/contracts/libraries/Bin.sol#L22

The function lpTokensFromDeltaReserve is vulnerable because it does not properly handle the case where either reserveA or reserveB is 0. This is a problem because when either of these variables is 0, the division operation self.state.totalSupply / self.state.reserveA or self.state.totalSupply / self.state.reserveB will result in a division by 0 error, which can cause the function to throw an exception or produce an incorrect result.

To mitigate this vulnerability, the function should check for the case where either reserveA or reserveB is 0, and handle it properly. For example, the function could return 0 for deltaAOptimal, deltaBOptimal, and proRataLiquidity in the case where either reserveA or reserveB is 0.

possibly like so :-
// Check if either reserveA or reserveB is 0
    if (self.state.reserveA == 0 || self.state.reserveB == 0) {
        // If either reserveA or reserveB is 0, set deltaAOptimal, deltaBOptimal, and proRataLiquidity to 0
        deltaAOptimal = 0;
        deltaBOptimal = 0;
        proRataLiquidity = 0;
    } else {
        // If neither reserveA nor reserveB is 0, continue with the original code...
        bool noA = self.state.reserveA == 0;
        bool noB = self.state.reserveB == 0;
