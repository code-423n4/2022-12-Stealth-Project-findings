1. Use errors instead of string in revert messages

2. Have positionMetadata functions in the same contract as Position.sol, since contract deployment costs alot of gas (32,000 gas)

3. Should use calldata for input paramteres instead of memory, if parameters are not changed in the function

