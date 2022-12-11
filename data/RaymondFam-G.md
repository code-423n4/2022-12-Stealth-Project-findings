## Split require statements using &&
Instead of using the `&&` operator in a single require statement to check multiple conditions, using multiple require statements with 1 condition per require statement will save 3 GAS per `&&`.

Here are some of the instances entailed:

[File: Factory.sol](https://github.com/code-423n4/2022-12-Stealth-Project/blob/main/maverick-v1/contracts/models/Factory.sol)

```
41:        require(msg.sender == owner && _owner != address(0), "Factory:NOT_ALLOWED");

60:        require(_fee > 0 && _fee < 1e18, "Factory:FEE_OUT_OF_BOUNDS");

62:        require(_lookback >= 3600 && _lookback <= uint16(type(int16).max), "Factory:LOOKBACK_OUT_OF_BOUNDS");
```
## Unused imports
Importing source units that are not referenced in the contract increases the size of deployment. Consider removing them to save some gas. If there was a plan to use them in the future, a comment explaining why they were imported would be helpful.

Here are some of the instances entailed:

[File: PoolInspector.sol#L7-L8](https://github.com/code-423n4/2022-12-Stealth-Project/blob/main/maverick-v1/contracts/models/PoolInspector.sol#L7-L8)

```
import "../libraries/Math.sol";
import "../libraries/Constants.sol";
```
## Non-strict inequalities are cheaper than strict ones
In the EVM, there is no opcode for non-strict inequalities (>=, <=) and two operations are performed (> + = or < + =).

As an example, consider replacing `>=` with the strict counterpart `>` in the following inequality instance:

[File: FixedPriceFactory.sol#L31](https://github.com/code-423n4/2022-12-escher/blob/main/src/minters/FixedPriceFactory.sol#L31)

```
- require(_lookback >= 3600 && _lookback <= uint16(type(int16).max), "Factory:LOOKBACK_OUT_OF_BOUNDS");
+ require(_lookback > 3600 - 1 && _lookback <= uint16(type(int16).max), "Factory:LOOKBACK_OUT_OF_BOUNDS");     
```
Similarly, consider replacing `<=` with the strict counterpart `<` in the following inequality instance, as an example:

[File: Factory.sol#L27](https://github.com/code-423n4/2022-12-Stealth-Project/blob/main/maverick-v1/contracts/models/Factory.sol#L27)

```
- require(_protocolFeeRatio <= ONE_3_DECIMAL_SCALE, "Factory:PROTOCOL_FEE_CANNOT_EXCEED_ONE");
+ require(_protocolFeeRatio < ONE_3_DECIMAL_SCALE + 1, "Factory:PROTOCOL_FEE_CANNOT_EXCEED_ONE");
```
## Use storage instead of memory for structs/arrays
A storage pointer is cheaper since copying a state struct in memory would incur as many SLOADs and MSTOREs as there are slots. In another words, this causes all fields of the struct/array to be read from storage, incurring a Gcoldsload (2100 gas) for each field of the struct/array, and then further incurring an additional MLOAD rather than a cheap stack read. As such, declaring the variable with the storage keyword and caching any fields that need to be re-read in stack variables will be much cheaper, involving only Gcoldsload for all associated field reads. Read the whole struct/array into a memory variable only when it is being returned by the function, passed into a function that requires memory, or if the array/struct is being read from another memory array/struct. 

Here are some of the instances entailed:

[File: Pool.sol](https://github.com/code-423n4/2022-12-Stealth-Project/blob/main/maverick-v1/contracts/models/Pool.sol)

```
118:        State memory currentState = checkReentrancy(false, true);

125:        AddLiquidityTemp memory temp;

128:            AddLiquidityParams memory param = params[i];

194:            RemoveLiquidityParams memory param = params[i];

225:            RemoveLiquidityParams memory param = params[i];

261:        State memory currentState = checkReentrancy(false, true);

264:        Delta.Instance memory delta;

278:            Delta.Instance memory newDelta;

390:                BinMap.Active memory active = moveData.activeList[i];

452:            MoveData memory moveData;

469:            MoveData memory moveData;

616:        uint128[] memory currentBinIds;
```
## += and -= cost more gas
`+=` and `-=` generally cost 22 more gas than writing out the assigned equation explicitly. The amount of gas wasted can be quite sizable when repeatedly operated in a loop.

For instance, the `+=` instances below may be refactored as follows:

[File: Pool.sol#L142-L143](https://github.com/code-423n4/2022-12-Stealth-Project/blob/main/maverick-v1/contracts/models/Pool.sol#L142-L143)

```
- tokenAAmount += temp.deltaA;
- tokenBAmount += temp.deltaB;
+ tokenAAmount = tokenAAmount + temp.deltaA;
+ tokenBAmount = tokenBAmount + temp.deltaB;
```
## `||` costs less gas than its equivalent `&&`
Rule of thumb: `(x && y)` is `(!(!x || !y))`

Even with the 10k Optimizer enabled: `||`, OR conditions cost less than their equivalent `&&`, AND conditions.

For instance, the instance below may be refactored as follows:

[File: Pool.sol#L233](https://github.com/code-423n4/2022-12-Stealth-Project/blob/main/maverick-v1/contracts/models/Pool.sol#L233)

```
- if (bin.state.reserveA == 0 && bin.state.reserveB == 0 && isActive) {
+ if (!(bin.state.reserveA != 0 || bin.state.reserveB != 0 || !isActive)) {
```
## Ternary over `if ... else`
Using ternary operator instead of the if else statement saves gas. 

For instance, the code block below may be refactored as follows:

[File: Pool.sol#L405-L410](https://github.com/code-423n4/2022-12-Stealth-Project/blob/main/maverick-v1/contracts/models/Pool.sol#L405-L410)

```
   currentBinId < moveData.firstBinId
       ? {
           moveData.firstBinId = currentBinId;
       }
       : {
           moveData.mergeBins[moveData.mergeBinCounter] = currentBinId;
           moveData.mergeBinCounter++;
       }
```
## Unchecked SafeMath saves gas
"Checked" math, which is default in ^0.8.0 is not free. The compiler will add some overflow checks, somehow similar to those implemented by `SafeMath`. While it is reasonable to expect these checks to be less expensive than the current `SafeMath`, one should keep in mind that these checks will increase the cost of "basic math operation" that were not previously covered. This particularly concerns variable increments in for loops. When no arithmetic overflow/underflow is going to happen, `unchecked { ++i ;}` to use the previous wrapping behavior further saves gas just as in the for loop below as an example:

[File: Pool.sol#L180-L183](https://github.com/code-423n4/2022-12-Stealth-Project/blob/main/maverick-v1/contracts/models/Pool.sol#L180-L183)

```
-        for (uint256 i; i < binIds.length; i++) {
+        for (uint256 i; i < binIds.length;) {
            Bin.Instance storage bin = bins[binIds[i]];
            bin.migrateBinsUpStack(bins, maxRecursion);

+            unchecked {
+                ++i;
+            }
        }
```
## Use of named returns for local variables saves gas
You can have further advantages in term of gas cost by simply using named return values as temporary local variable.

For instance, the code block instance below may be refactored as follows:

[File: Pool.sol#L97-L99](https://github.com/code-423n4/2022-12-Stealth-Project/blob/main/maverick-v1/contracts/models/Pool.sol#L97-L99)

```
-    function getState() external view override returns (State memory) {
-        return state;
+    function getState() external view override returns (State memory _state) {
+        _state = state;
```
## Function Order Affects Gas Consumption
The order of function will also have an impact on gas consumption. Because in smart contracts, there is a difference in the order of the functions. Each position will have an extra 22 gas. The order is dependent on method ID. So, if you rename the frequently accessed function to more early method ID, you can save gas cost. Please visit the following site for further information:

https://medium.com/joyso/solidity-how-does-function-name-affect-gas-consumption-in-smart-contract-47d270d8ac92

## Activate the Optimizer
Before deploying your contract, activate the optimizer when compiling using “solc --optimize --bin sourceFile.sol”. By default, the optimizer will optimize the contract assuming it is called 200 times across its lifetime. If you want the initial contract deployment to be cheaper and the later function executions to be more expensive, set it to “ --optimize-runs=1”. Conversely, if you expect many transactions and do not care for higher deployment cost and output size, set “--optimize-runs” to a high number.

```
module.exports = {
  solidity: {
    version: "0.8.0",
    settings: {
      optimizer: {
        enabled: true,
        runs: 1000,
      },
    },
  },
};
```
Please visit the following site for further information:

https://docs.soliditylang.org/en/v0.5.4/using-the-compiler.html#using-the-commandline-compiler

Here's one example of instance on opcode comparison that delineates the gas saving mechanism:

```
for !=0 before optimization
PUSH1 0x00
DUP2
EQ
ISZERO
PUSH1 [cont offset]
JUMPI

after optimization
DUP1
PUSH1 [revert offset]
JUMPI
```
Disclaimer: There have been several bugs with security implications related to optimizations. For this reason, Solidity compiler optimizations are disabled by default, and it is unclear how many contracts in the wild actually use them. Therefore, it is unclear how well they are being tested and exercised. High-severity security issues due to optimization bugs have occurred in the past . A high-severity bug in the emscripten -generated solc-js compiler used by Truffle and Remix persisted until late 2018. The fix for this bug was not reported in the Solidity CHANGELOG. Another high-severity optimization bug resulting in incorrect bit shift results was patched in Solidity 0.5.6. Please measure the gas savings from optimizations, and carefully weigh them against the possibility of an optimization-related bug. Also, monitor the development and adoption of Solidity compiler optimizations to assess their maturity.

## Payable Access Control Functions Costs Less Gas
Consider marking functions with access control as `payable`. This will save 20 gas on each call by their respective permissible callers for not needing to have the compiler check for `msg.value`.

Here are some of the instances entailed:

[File: Position.sol#L23](https://github.com/code-423n4/2022-12-Stealth-Project/blob/main/maverick-v1/contracts/models/Position.sol#L23)

```
    function setMetadata(IPositionMetadata _metadata) external onlyOwner {
```