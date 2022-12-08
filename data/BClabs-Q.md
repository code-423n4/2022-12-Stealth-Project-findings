1. should emit event on major changes

2. Should be using the latest solidity version 0.8.17 instead of 0.8.0

3. Could use enums for action and lock/emergency constants to improve readability

4. Comment in IPool line 60 typo

5. Kinds described in IPool should have values from 1 to 4, but the kinds in constants have values 1,2,4,8. This can be confusing, so try unifying the values.
