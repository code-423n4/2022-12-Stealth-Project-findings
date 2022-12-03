# [N-01] AVOID NESTED IF BLOCKS

For better readability and analysis it is better to avoid nested if blocks. Here is an example:



 if (activeTick > startingTickInitial || floorTwap > lastTwap) {
            MoveData memory moveData;
            int32 startingTick = int32(Math.min(startingTickInitial, lastTwap));

  moveData.tickLimit = int32(Math.min(activeTick - 1, floorTwap));
  moveData.kinds = [1, 3];

  if (startingTick - 1 < moveData.tickLimit) {


- https://github.com/code-423n4/2022-12-Stealth-Project/blob/f985239db0ceb83c0c0931623607f603ad874a22/maverick-v1/contracts/models/Pool.sol#L415

if (amountIn.mul(PRBMathUD60x18.SCALE - fee) >= binAmountIn) {
            feeBasis = Math.mulDiv(binAmountIn, fee, PRBMathUD60x18.SCALE - fee, true);
            delta.deltaInErc = binAmountIn + feeBasis;
            if (limitInBin) {
                delta.swappedToMaxPrice = true;
            }

- https://github.com/code-423n4/2022-12-Stealth-Project/blob/f985239db0ceb83c0c0931623607f603ad874a22/maverick-v1/contracts/models/Pool.sol#L516


# [N-02] Constants should be defined rather than using magic numbers

File: Factory.sol

require(_fee > 0 && _fee < `1e18`, "Factory:FEE_OUT_OF_BOUNDS");

require(_lookback >= `3600` && _lookback <= uint16(type(int16).max), "Factory:LOOKBACK_OUT_OF_BOUNDS");

Lines of code:

- https://github.com/code-423n4/2022-12-Stealth-Project/blob/1fd474ff560404b7ea4ac4c492397a4571481814/maverick-v1/contracts/models/Factory.sol#L60

- https://github.com/code-423n4/2022-12-Stealth-Project/blob/1fd474ff560404b7ea4ac4c492397a4571481814/maverick-v1/contracts/models/Factory.sol#L62

# [N-03] Missing/incomplete natspec comments

Multiple contracts are missing natspec comments which makes code more difficult to read and prone to errors:

Pool.sol, PoolInspector.sol, Position.sol, PositionMetadata.sol

# [N-04] Typo in comments

File: IRouter.sol

/// @param poolParams paramters of a pool
/// @param addParams paramters of liquidity addition
/// @param params paramters of liquidity addition
/// @param params paramters of liquidity addition
/// @param params paramters of liquidity removal

Consider changing `paramters` with parameters

Lines of code:

- https://github.com/code-423n4/2022-12-Stealth-Project/blob/fc8589d7d8c1d8488fd97ccc46e1ff11c8426ac2/router-v1/contracts/interfaces/IRouter.sol#L114

- https://github.com/code-423n4/2022-12-Stealth-Project/blob/fc8589d7d8c1d8488fd97ccc46e1ff11c8426ac2/router-v1/contracts/interfaces/IRouter.sol#L116

- https://github.com/code-423n4/2022-12-Stealth-Project/blob/fc8589d7d8c1d8488fd97ccc46e1ff11c8426ac2/router-v1/contracts/interfaces/IRouter.sol#L132

- https://github.com/code-423n4/2022-12-Stealth-Project/blob/fc8589d7d8c1d8488fd97ccc46e1ff11c8426ac2/router-v1/contracts/interfaces/IRouter.sol#L148

- https://github.com/code-423n4/2022-12-Stealth-Project/blob/fc8589d7d8c1d8488fd97ccc46e1ff11c8426ac2/router-v1/contracts/interfaces/IRouter.sol#L177

File: IPool.sol

/// @notice return paramters for Add/Remove liquidity - parameters*
//mergeId is the currrent active bin. - current*
/// @param maxRecursion is the maximum rerursion depth of the migration. set to - recursion depth*

Lines of code:

- https://github.com/code-423n4/2022-12-Stealth-Project/blob/fc8589d7d8c1d8488fd97ccc46e1ff11c8426ac2/maverick-v1/contracts/interfaces/IPool.sol#L26

- https://github.com/code-423n4/2022-12-Stealth-Project/blob/fc8589d7d8c1d8488fd97ccc46e1ff11c8426ac2/maverick-v1/contracts/interfaces/IPool.sol#L167

- https://github.com/code-423n4/2022-12-Stealth-Project/blob/fc8589d7d8c1d8488fd97ccc46e1ff11c8426ac2/maverick-v1/contracts/interfaces/IPool.sol#L169



#  [L-01] Use the latest version of OpenZeppelin

To prevent any issues in the future (e.g. using solely hardhat to compile and deploy the contracts), upgrade the used OZ packages within the package.json to the latest versions. Currently the project is using floating version. 

Upgrade it to stable 4.8.0 (which is the newest)

Lines of code:

- https://github.com/code-423n4/2022-12-Stealth-Project/blob/fc8589d7d8c1d8488fd97ccc46e1ff11c8426ac2/maverick-v1/package.json#L71





