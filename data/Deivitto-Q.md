
## Single-step process for critical ownership transfer/renounce is risky
### Impact
The following contracts and functions, allow owners to interact with functions such as:
`Position.sol#setMetadata()`
`PositionMetadata.sol#setBaseURI()`

Given that `Position.sol` and `PositionMetadata.sol` are derived from `Ownable`, the ownership management of this contract defaults to `Ownable` ’s `transferOwnership()` and `renounceOwnership()` methods which are not overridden here. 

Such critical address transfer/renouncing in one-step is very risky because it is irrecoverable from any mistakes

Scenario: If an incorrect address, e.g. for which the private key is not known, is used accidentally then it prevents the use of all the `onlyOwner()` functions forever, which includes the changing of various critical addresses and parameters. This use of incorrect address may not even be immediately apparent given that these functions are probably not used immediately. 

When noticed, due to a failing `onlyOwner()` function call, it will force the redeployment of these contracts and require appropriate changes and notifications for switching from the old to new address. This will diminish trust in the protocol and incur a significant reputational damage.

### Github Permalinks
- `Ownable`
https://github.com/code-423n4/2022-12-Stealth-Project/blob/2c798a70333c83102affd19c4346412be4bf859e/maverick-v1/contracts/models/Position.sol#L12
contract Position is ERC721, ERC721Enumerable, Ownable, IPosition {

https://github.com/code-423n4/2022-12-Stealth-Project/blob/2c798a70333c83102affd19c4346412be4bf859e/maverick-v1/contracts/models/PositionMetadata.sol#L8
contract PositionMetadata is IPositionMetadata, Ownable {

### Proof of Concept
See similar High Risk severity finding from Trail-of-Bits Audit of Hermez.
https://github.com/trailofbits/publications/blob/master/reviews/hermez.pdf
See similar Medium Risk severity finding from Trail-of-Bits Audit of Uniswap V3:
https://github.com/Uniswap/v3-core/blob/main/audits/tob/audit.pdf

### Recommended steps
Recommend overriding the inherited methods to null functions and use separate functions for a two-step address change:
1. Approve a new address as a `pendingOwner`
2. A transaction from the `pendingOwner` address claims the pending ownership change.

This mitigates risk because if an incorrect address is used in step (1) then it can be fixed by re-approving the correct address. Only after a correct address is used in step (1) can step (2) happen and complete the address/ownership change.

Also, consider adding a time-delay for such sensitive actions. And at a minimum, use a multisig owner address and not an EOA.

## Missing checks for address(0x0) when assigning values to `immutable` variables 
### Summary
Zero address should be checked for state variables, immutable variables. A zero address can lead into problems.
### Github Permalinks
```
    constructor(IFactory _factory, IPosition _position, IWETH9 _WETH9) {
        factory = _factory;
        position = _position;
        WETH9 = _WETH9;
    }
```
https://github.com/code-423n4/2022-12-Stealth-Project/blob/2c798a70333c83102affd19c4346412be4bf859e/router-v1/contracts/Router.sol#L21

### Mitigation
Check zero address before assigning or using it


# Informational
## Inconsistent spacing in comments
Some lines use `// x` and some use `//x`. The instances below point out the usages that don't follow the common way, within each file
### Details
`uint256 public genericDeclaration;//generic comment without space`
But following the style of the other comments would be:
`uint256 public genericDeclaration; // generic comment with space`

### Common usage of `// `
  // The multiplication in the next line has the same exact purpose
    // as the one above.
https://github.com/code-423n4/2022-12-Stealth-Project/blob/2c798a70333c83102affd19c4346412be4bf859e/router-v1/contracts/libraries/BytesLib.sol#L44    
### Usage of `//comment`
https://github.com/code-423n4/2022-12-Stealth-Project/blob/2c798a70333c83102affd19c4346412be4bf859e/maverick-v1/contracts/interfaces/IFactory.sol#L29
    //protocol

https://github.com/code-423n4/2022-12-Stealth-Project/blob/2c798a70333c83102affd19c4346412be4bf859e/maverick-v1/contracts/interfaces/IPool.sol#L78
    //to the current bin or an absolute position

https://github.com/code-423n4/2022-12-Stealth-Project/blob/2c798a70333c83102affd19c4346412be4bf859e/maverick-v1/contracts/interfaces/IPool.sol#L100
    //defined in Pool.sol

https://github.com/code-423n4/2022-12-Stealth-Project/blob/2c798a70333c83102affd19c4346412be4bf859e/maverick-v1/contracts/interfaces/IPool.sol#L103
    //protocol

https://github.com/code-423n4/2022-12-Stealth-Project/blob/2c798a70333c83102affd19c4346412be4bf859e/maverick-v1/contracts/interfaces/IPool.sol#L147
    //position of the liquidity

https://github.com/code-423n4/2022-12-Stealth-Project/blob/2c798a70333c83102affd19c4346412be4bf859e/maverick-v1/contracts/interfaces/IPool.sol#L149
    //caller can transfer tokens

https://github.com/code-423n4/2022-12-Stealth-Project/blob/2c798a70333c83102affd19c4346412be4bf859e/maverick-v1/contracts/interfaces/IPool.sol#L153
    //to another

https://github.com/code-423n4/2022-12-Stealth-Project/blob/2c798a70333c83102affd19c4346412be4bf859e/maverick-v1/contracts/interfaces/IPool.sol#L163
    //and amounts

https://github.com/code-423n4/2022-12-Stealth-Project/blob/2c798a70333c83102affd19c4346412be4bf859e/maverick-v1/contracts/interfaces/IPool.sol#L167
    //mergeId is the currrent active bin.

https://github.com/code-423n4/2022-12-Stealth-Project/blob/2c798a70333c83102affd19c4346412be4bf859e/maverick-v1/contracts/interfaces/IPool.sol#L170
    //zero to recurse until the active bin is found.

https://github.com/code-423n4/2022-12-Stealth-Project/blob/2c798a70333c83102affd19c4346412be4bf859e/maverick-v1/contracts/interfaces/IPool.sol#L176
    //is false or the output if exactOutput is true

https://github.com/code-423n4/2022-12-Stealth-Project/blob/2c798a70333c83102affd19c4346412be4bf859e/maverick-v1/contracts/interfaces/IPool.sol#L179
    //exact output amount (true)

https://github.com/code-423n4/2022-12-Stealth-Project/blob/2c798a70333c83102affd19c4346412be4bf859e/maverick-v1/contracts/interfaces/IPool.sol#L181
    //indicates no limit.  Limit is only engaged for exactOutput=false.  If the

https://github.com/code-423n4/2022-12-Stealth-Project/blob/2c798a70333c83102affd19c4346412be4bf859e/maverick-v1/contracts/interfaces/IPool.sol#L182
    //limit is reached only part of the input amount will be swapped and the

https://github.com/code-423n4/2022-12-Stealth-Project/blob/2c798a70333c83102affd19c4346412be4bf859e/maverick-v1/contracts/interfaces/IPool.sol#L183
    //callback will only require that amount of the swap to be paid.

https://github.com/code-423n4/2022-12-Stealth-Project/blob/2c798a70333c83102affd19c4346412be4bf859e/maverick-v1/contracts/interfaces/IPool.sol#L185
    //caller can transfer tokens

https://github.com/code-423n4/2022-12-Stealth-Project/blob/2c798a70333c83102affd19c4346412be4bf859e/maverick-v1/contracts/interfaces/IPoolAdmin.sol#L7
    //or B protocol fee (2 or 3)

https://github.com/code-423n4/2022-12-Stealth-Project/blob/2c798a70333c83102affd19c4346412be4bf859e/router-v1/contracts/Router.sol#L21
    //computed amount in for an exact output swap / can never actually be this

https://github.com/code-423n4/2022-12-Stealth-Project/blob/2c798a70333c83102affd19c4346412be4bf859e/router-v1/contracts/Router.sol#L22
    //value

https://github.com/code-423n4/2022-12-Stealth-Project/blob/2c798a70333c83102affd19c4346412be4bf859e/router-v1/contracts/libraries/BytesLib.sol#L56
                //update free-memory pointer

https://github.com/code-423n4/2022-12-Stealth-Project/blob/2c798a70333c83102affd19c4346412be4bf859e/router-v1/contracts/libraries/BytesLib.sol#L57
                //allocating the array padded to 32 bytes like the compiler does now

https://github.com/code-423n4/2022-12-Stealth-Project/blob/2c798a70333c83102affd19c4346412be4bf859e/router-v1/contracts/libraries/BytesLib.sol#L60
            //if we want a zero-length slice let's just return a zero-length array

https://github.com/code-423n4/2022-12-Stealth-Project/blob/2c798a70333c83102affd19c4346412be4bf859e/router-v1/contracts/libraries/BytesLib.sol#L63
                //zero out the 32 bytes slice we are about to return

https://github.com/code-423n4/2022-12-Stealth-Project/blob/2c798a70333c83102affd19c4346412be4bf859e/router-v1/contracts/libraries/BytesLib.sol#L64
                //we need to do it because Solidity does not garbage collect

https://github.com/code-423n4/2022-12-Stealth-Project/blob/2c798a70333c83102affd19c4346412be4bf859e/router-v1/contracts/libraries/Multicall.sol#L17
                // Next 5 lines from https://ethereum.stackexchange.com/a/83577

https://github.com/code-423n4/2022-12-Stealth-Project/blob/2c798a70333c83102affd19c4346412be4bf859e/router-v1/contracts/interfaces/IRouter.sol#L33
    //another token

https://github.com/code-423n4/2022-12-Stealth-Project/blob/2c798a70333c83102affd19c4346412be4bf859e/router-v1/contracts/interfaces/IRouter.sol#L48
    //another along the specified path

https://github.com/code-423n4/2022-12-Stealth-Project/blob/2c798a70333c83102affd19c4346412be4bf859e/router-v1/contracts/interfaces/IRouter.sol#L50
    //as `ExactInputParams` in calldata

https://github.com/code-423n4/2022-12-Stealth-Project/blob/2c798a70333c83102affd19c4346412be4bf859e/router-v1/contracts/interfaces/IRouter.sol#L65
    //another token

https://github.com/code-423n4/2022-12-Stealth-Project/blob/2c798a70333c83102affd19c4346412be4bf859e/router-v1/contracts/interfaces/IRouter.sol#L80
    //another along the specified path (reversed)

https://github.com/code-423n4/2022-12-Stealth-Project/blob/2c798a70333c83102affd19c4346412be4bf859e/router-v1/contracts/interfaces/IRouter.sol#L82
    //as `ExactOutputParams` in calldata



## Maximum line length exceeded
### Summary
Long lines should be wrapped to conform with Solidity Style guidelines. 
### Details 
Lines that exceed the 120 character length suggested by the Solidity Style guidelines. Reference: https://docs.soliditylang.org/en/v0.8.16/style-guide.html#maximum-line-length
### Github Permalinks 


https://github.com/code-423n4/2022-12-Stealth-Project/blob/2c798a70333c83102affd19c4346412be4bf859e/maverick-v1/contracts/models/Factory.sol#L46

https://github.com/code-423n4/2022-12-Stealth-Project/blob/2c798a70333c83102affd19c4346412be4bf859e/maverick-v1/contracts/models/Factory.sol#L58

https://github.com/code-423n4/2022-12-Stealth-Project/blob/2c798a70333c83102affd19c4346412be4bf859e/maverick-v1/contracts/models/Factory.sol#L64

https://github.com/code-423n4/2022-12-Stealth-Project/blob/2c798a70333c83102affd19c4346412be4bf859e/maverick-v1/contracts/models/Factory.sol#L80

https://github.com/code-423n4/2022-12-Stealth-Project/blob/2c798a70333c83102affd19c4346412be4bf859e/maverick-v1/contracts/models/Pool.sol#L93

https://github.com/code-423n4/2022-12-Stealth-Project/blob/2c798a70333c83102affd19c4346412be4bf859e/maverick-v1/contracts/models/Pool.sol#L131

https://github.com/code-423n4/2022-12-Stealth-Project/blob/2c798a70333c83102affd19c4346412be4bf859e/maverick-v1/contracts/models/Pool.sol#L168

https://github.com/code-423n4/2022-12-Stealth-Project/blob/2c798a70333c83102affd19c4346412be4bf859e/maverick-v1/contracts/models/Pool.sol#L187

https://github.com/code-423n4/2022-12-Stealth-Project/blob/2c798a70333c83102affd19c4346412be4bf859e/maverick-v1/contracts/models/Pool.sol#L238

https://github.com/code-423n4/2022-12-Stealth-Project/blob/2c798a70333c83102affd19c4346412be4bf859e/maverick-v1/contracts/models/Pool.sol#L260

https://github.com/code-423n4/2022-12-Stealth-Project/blob/2c798a70333c83102affd19c4346412be4bf859e/maverick-v1/contracts/models/Pool.sol#L270

https://github.com/code-423n4/2022-12-Stealth-Project/blob/2c798a70333c83102affd19c4346412be4bf859e/maverick-v1/contracts/models/Pool.sol#L283

https://github.com/code-423n4/2022-12-Stealth-Project/blob/2c798a70333c83102affd19c4346412be4bf859e/maverick-v1/contracts/models/Pool.sol#L294

https://github.com/code-423n4/2022-12-Stealth-Project/blob/2c798a70333c83102affd19c4346412be4bf859e/maverick-v1/contracts/models/Pool.sol#L393

https://github.com/code-423n4/2022-12-Stealth-Project/blob/2c798a70333c83102affd19c4346412be4bf859e/maverick-v1/contracts/models/Pool.sol#L426

https://github.com/code-423n4/2022-12-Stealth-Project/blob/2c798a70333c83102affd19c4346412be4bf859e/maverick-v1/contracts/models/Pool.sol#L435

https://github.com/code-423n4/2022-12-Stealth-Project/blob/2c798a70333c83102affd19c4346412be4bf859e/maverick-v1/contracts/models/Pool.sol#L486

https://github.com/code-423n4/2022-12-Stealth-Project/blob/2c798a70333c83102affd19c4346412be4bf859e/maverick-v1/contracts/models/Pool.sol#L516

https://github.com/code-423n4/2022-12-Stealth-Project/blob/2c798a70333c83102affd19c4346412be4bf859e/maverick-v1/contracts/models/Pool.sol#L534

https://github.com/code-423n4/2022-12-Stealth-Project/blob/2c798a70333c83102affd19c4346412be4bf859e/maverick-v1/contracts/models/Pool.sol#L537

https://github.com/code-423n4/2022-12-Stealth-Project/blob/2c798a70333c83102affd19c4346412be4bf859e/maverick-v1/contracts/models/Pool.sol#L550

https://github.com/code-423n4/2022-12-Stealth-Project/blob/2c798a70333c83102affd19c4346412be4bf859e/maverick-v1/contracts/models/Pool.sol#L565

https://github.com/code-423n4/2022-12-Stealth-Project/blob/2c798a70333c83102affd19c4346412be4bf859e/maverick-v1/contracts/models/Pool.sol#L589

https://github.com/code-423n4/2022-12-Stealth-Project/blob/2c798a70333c83102affd19c4346412be4bf859e/maverick-v1/contracts/models/Pool.sol#L597

https://github.com/code-423n4/2022-12-Stealth-Project/blob/2c798a70333c83102affd19c4346412be4bf859e/maverick-v1/contracts/models/Pool.sol#L602

https://github.com/code-423n4/2022-12-Stealth-Project/blob/2c798a70333c83102affd19c4346412be4bf859e/maverick-v1/contracts/models/Pool.sol#L614

https://github.com/code-423n4/2022-12-Stealth-Project/blob/2c798a70333c83102affd19c4346412be4bf859e/maverick-v1/contracts/models/Pool.sol#L625

https://github.com/code-423n4/2022-12-Stealth-Project/blob/2c798a70333c83102affd19c4346412be4bf859e/maverick-v1/contracts/models/Pool.sol#L636

https://github.com/code-423n4/2022-12-Stealth-Project/blob/2c798a70333c83102affd19c4346412be4bf859e/maverick-v1/contracts/models/Pool.sol#L637

https://github.com/code-423n4/2022-12-Stealth-Project/blob/2c798a70333c83102affd19c4346412be4bf859e/maverick-v1/contracts/models/Pool.sol#L639

https://github.com/code-423n4/2022-12-Stealth-Project/blob/2c798a70333c83102affd19c4346412be4bf859e/maverick-v1/contracts/models/PoolInspector.sol#L23

https://github.com/code-423n4/2022-12-Stealth-Project/blob/2c798a70333c83102affd19c4346412be4bf859e/maverick-v1/contracts/models/PoolInspector.sol#L33

https://github.com/code-423n4/2022-12-Stealth-Project/blob/2c798a70333c83102affd19c4346412be4bf859e/maverick-v1/contracts/models/PoolInspector.sol#L64

https://github.com/code-423n4/2022-12-Stealth-Project/blob/2c798a70333c83102affd19c4346412be4bf859e/maverick-v1/contracts/models/PoolInspector.sol#L65

https://github.com/code-423n4/2022-12-Stealth-Project/blob/2c798a70333c83102affd19c4346412be4bf859e/maverick-v1/contracts/models/PoolInspector.sol#L73

https://github.com/code-423n4/2022-12-Stealth-Project/blob/2c798a70333c83102affd19c4346412be4bf859e/maverick-v1/contracts/models/PoolInspector.sol#L95

https://github.com/code-423n4/2022-12-Stealth-Project/blob/2c798a70333c83102affd19c4346412be4bf859e/maverick-v1/contracts/models/PoolInspector.sol#L107

https://github.com/code-423n4/2022-12-Stealth-Project/blob/2c798a70333c83102affd19c4346412be4bf859e/maverick-v1/contracts/models/Position.sol#L34

https://github.com/code-423n4/2022-12-Stealth-Project/blob/2c798a70333c83102affd19c4346412be4bf859e/maverick-v1/contracts/models/Position.sol#L38

https://github.com/code-423n4/2022-12-Stealth-Project/blob/2c798a70333c83102affd19c4346412be4bf859e/maverick-v1/contracts/libraries/Bin.sol#L37

https://github.com/code-423n4/2022-12-Stealth-Project/blob/2c798a70333c83102affd19c4346412be4bf859e/maverick-v1/contracts/libraries/Bin.sol#L40

https://github.com/code-423n4/2022-12-Stealth-Project/blob/2c798a70333c83102affd19c4346412be4bf859e/maverick-v1/contracts/libraries/Bin.sol#L52

https://github.com/code-423n4/2022-12-Stealth-Project/blob/2c798a70333c83102affd19c4346412be4bf859e/maverick-v1/contracts/libraries/Bin.sol#L56

https://github.com/code-423n4/2022-12-Stealth-Project/blob/2c798a70333c83102affd19c4346412be4bf859e/maverick-v1/contracts/libraries/Bin.sol#L84

https://github.com/code-423n4/2022-12-Stealth-Project/blob/2c798a70333c83102affd19c4346412be4bf859e/maverick-v1/contracts/libraries/Bin.sol#L94

https://github.com/code-423n4/2022-12-Stealth-Project/blob/2c798a70333c83102affd19c4346412be4bf859e/maverick-v1/contracts/libraries/Bin.sol#L110

https://github.com/code-423n4/2022-12-Stealth-Project/blob/2c798a70333c83102affd19c4346412be4bf859e/maverick-v1/contracts/libraries/Bin.sol#L121

https://github.com/code-423n4/2022-12-Stealth-Project/blob/2c798a70333c83102affd19c4346412be4bf859e/maverick-v1/contracts/libraries/Bin.sol#L146

https://github.com/code-423n4/2022-12-Stealth-Project/blob/2c798a70333c83102affd19c4346412be4bf859e/maverick-v1/contracts/libraries/Bin.sol#L151

https://github.com/code-423n4/2022-12-Stealth-Project/blob/2c798a70333c83102affd19c4346412be4bf859e/maverick-v1/contracts/libraries/Bin.sol#L152

https://github.com/code-423n4/2022-12-Stealth-Project/blob/2c798a70333c83102affd19c4346412be4bf859e/maverick-v1/contracts/libraries/BinMap.sol#L35

https://github.com/code-423n4/2022-12-Stealth-Project/blob/2c798a70333c83102affd19c4346412be4bf859e/maverick-v1/contracts/libraries/BinMap.sol#L41

https://github.com/code-423n4/2022-12-Stealth-Project/blob/2c798a70333c83102affd19c4346412be4bf859e/maverick-v1/contracts/libraries/BinMap.sol#L75

https://github.com/code-423n4/2022-12-Stealth-Project/blob/2c798a70333c83102affd19c4346412be4bf859e/maverick-v1/contracts/libraries/BinMath.sol#L121

https://github.com/code-423n4/2022-12-Stealth-Project/blob/2c798a70333c83102affd19c4346412be4bf859e/maverick-v1/contracts/libraries/BinMath.sol#L127

https://github.com/code-423n4/2022-12-Stealth-Project/blob/2c798a70333c83102affd19c4346412be4bf859e/maverick-v1/contracts/libraries/BinMath.sol#L134

https://github.com/code-423n4/2022-12-Stealth-Project/blob/2c798a70333c83102affd19c4346412be4bf859e/maverick-v1/contracts/libraries/BinMath.sol#L142

https://github.com/code-423n4/2022-12-Stealth-Project/blob/2c798a70333c83102affd19c4346412be4bf859e/maverick-v1/contracts/libraries/Delta.sol#L35

https://github.com/code-423n4/2022-12-Stealth-Project/blob/2c798a70333c83102affd19c4346412be4bf859e/maverick-v1/contracts/libraries/Twa.sol#L32

https://github.com/code-423n4/2022-12-Stealth-Project/blob/2c798a70333c83102affd19c4346412be4bf859e/maverick-v1/contracts/interfaces/IFactory.sol#L9

https://github.com/code-423n4/2022-12-Stealth-Project/blob/2c798a70333c83102affd19c4346412be4bf859e/maverick-v1/contracts/interfaces/IFactory.sol#L20

https://github.com/code-423n4/2022-12-Stealth-Project/blob/2c798a70333c83102affd19c4346412be4bf859e/maverick-v1/contracts/interfaces/IFactory.sol#L22

https://github.com/code-423n4/2022-12-Stealth-Project/blob/2c798a70333c83102affd19c4346412be4bf859e/maverick-v1/contracts/interfaces/IPool.sol#L8

https://github.com/code-423n4/2022-12-Stealth-Project/blob/2c798a70333c83102affd19c4346412be4bf859e/maverick-v1/contracts/interfaces/IPool.sol#L16

https://github.com/code-423n4/2022-12-Stealth-Project/blob/2c798a70333c83102affd19c4346412be4bf859e/maverick-v1/contracts/interfaces/IPool.sol#L150

https://github.com/code-423n4/2022-12-Stealth-Project/blob/2c798a70333c83102affd19c4346412be4bf859e/maverick-v1/contracts/interfaces/IPool.sol#L157

https://github.com/code-423n4/2022-12-Stealth-Project/blob/2c798a70333c83102affd19c4346412be4bf859e/maverick-v1/contracts/interfaces/IPool.sol#L164

https://github.com/code-423n4/2022-12-Stealth-Project/blob/2c798a70333c83102affd19c4346412be4bf859e/maverick-v1/contracts/interfaces/IPool.sol#L186

https://github.com/code-423n4/2022-12-Stealth-Project/blob/2c798a70333c83102affd19c4346412be4bf859e/router-v1/contracts/Router.sol#L121

https://github.com/code-423n4/2022-12-Stealth-Project/blob/2c798a70333c83102affd19c4346412be4bf859e/router-v1/contracts/Router.sol#L132

https://github.com/code-423n4/2022-12-Stealth-Project/blob/2c798a70333c83102affd19c4346412be4bf859e/router-v1/contracts/Router.sol#L137

https://github.com/code-423n4/2022-12-Stealth-Project/blob/2c798a70333c83102affd19c4346412be4bf859e/router-v1/contracts/Router.sol#L143

https://github.com/code-423n4/2022-12-Stealth-Project/blob/2c798a70333c83102affd19c4346412be4bf859e/router-v1/contracts/Router.sol#L169

https://github.com/code-423n4/2022-12-Stealth-Project/blob/2c798a70333c83102affd19c4346412be4bf859e/router-v1/contracts/Router.sol#L181

https://github.com/code-423n4/2022-12-Stealth-Project/blob/2c798a70333c83102affd19c4346412be4bf859e/router-v1/contracts/Router.sol#L185

https://github.com/code-423n4/2022-12-Stealth-Project/blob/2c798a70333c83102affd19c4346412be4bf859e/router-v1/contracts/Router.sol#L193

https://github.com/code-423n4/2022-12-Stealth-Project/blob/2c798a70333c83102affd19c4346412be4bf859e/router-v1/contracts/Router.sol#L194

https://github.com/code-423n4/2022-12-Stealth-Project/blob/2c798a70333c83102affd19c4346412be4bf859e/router-v1/contracts/Router.sol#L221

https://github.com/code-423n4/2022-12-Stealth-Project/blob/2c798a70333c83102affd19c4346412be4bf859e/router-v1/contracts/Router.sol#L231

https://github.com/code-423n4/2022-12-Stealth-Project/blob/2c798a70333c83102affd19c4346412be4bf859e/router-v1/contracts/Router.sol#L245

https://github.com/code-423n4/2022-12-Stealth-Project/blob/2c798a70333c83102affd19c4346412be4bf859e/router-v1/contracts/Router.sol#L259

https://github.com/code-423n4/2022-12-Stealth-Project/blob/2c798a70333c83102affd19c4346412be4bf859e/router-v1/contracts/Router.sol#L269

https://github.com/code-423n4/2022-12-Stealth-Project/blob/2c798a70333c83102affd19c4346412be4bf859e/router-v1/contracts/Router.sol#L272

https://github.com/code-423n4/2022-12-Stealth-Project/blob/2c798a70333c83102affd19c4346412be4bf859e/router-v1/contracts/Router.sol#L284

https://github.com/code-423n4/2022-12-Stealth-Project/blob/2c798a70333c83102affd19c4346412be4bf859e/router-v1/contracts/Router.sol#L290

https://github.com/code-423n4/2022-12-Stealth-Project/blob/2c798a70333c83102affd19c4346412be4bf859e/router-v1/contracts/Router.sol#L303

https://github.com/code-423n4/2022-12-Stealth-Project/blob/2c798a70333c83102affd19c4346412be4bf859e/router-v1/contracts/libraries/SelfPermit.sol#L12

https://github.com/code-423n4/2022-12-Stealth-Project/blob/2c798a70333c83102affd19c4346412be4bf859e/router-v1/contracts/libraries/SelfPermit.sol#L16

https://github.com/code-423n4/2022-12-Stealth-Project/blob/2c798a70333c83102affd19c4346412be4bf859e/router-v1/contracts/libraries/SelfPermit.sol#L21

https://github.com/code-423n4/2022-12-Stealth-Project/blob/2c798a70333c83102affd19c4346412be4bf859e/router-v1/contracts/libraries/SelfPermit.sol#L26

https://github.com/code-423n4/2022-12-Stealth-Project/blob/2c798a70333c83102affd19c4346412be4bf859e/router-v1/contracts/libraries/SelfPermit.sol#L31

https://github.com/code-423n4/2022-12-Stealth-Project/blob/2c798a70333c83102affd19c4346412be4bf859e/router-v1/contracts/libraries/SelfPermit.sol#L32

https://github.com/code-423n4/2022-12-Stealth-Project/blob/2c798a70333c83102affd19c4346412be4bf859e/router-v1/contracts/libraries/TransferHelper.sol#L14

https://github.com/code-423n4/2022-12-Stealth-Project/blob/2c798a70333c83102affd19c4346412be4bf859e/router-v1/contracts/interfaces/IMulticall.sol#L8

https://github.com/code-423n4/2022-12-Stealth-Project/blob/2c798a70333c83102affd19c4346412be4bf859e/router-v1/contracts/interfaces/IRouter.sol#L127

https://github.com/code-423n4/2022-12-Stealth-Project/blob/2c798a70333c83102affd19c4346412be4bf859e/router-v1/contracts/interfaces/IRouter.sol#L143

https://github.com/code-423n4/2022-12-Stealth-Project/blob/2c798a70333c83102affd19c4346412be4bf859e/router-v1/contracts/interfaces/IRouter.sol#L163

https://github.com/code-423n4/2022-12-Stealth-Project/blob/2c798a70333c83102affd19c4346412be4bf859e/router-v1/contracts/interfaces/ISelfPermit.sol#L15

https://github.com/code-423n4/2022-12-Stealth-Project/blob/2c798a70333c83102affd19c4346412be4bf859e/router-v1/contracts/interfaces/ISelfPermit.sol#L26

https://github.com/code-423n4/2022-12-Stealth-Project/blob/2c798a70333c83102affd19c4346412be4bf859e/router-v1/contracts/interfaces/ISelfPermit.sol#L28

https://github.com/code-423n4/2022-12-Stealth-Project/blob/2c798a70333c83102affd19c4346412be4bf859e/router-v1/contracts/interfaces/ISelfPermit.sol#L36

https://github.com/code-423n4/2022-12-Stealth-Project/blob/2c798a70333c83102affd19c4346412be4bf859e/router-v1/contracts/interfaces/ISelfPermit.sol#L38

https://github.com/code-423n4/2022-12-Stealth-Project/blob/2c798a70333c83102affd19c4346412be4bf859e/router-v1/contracts/interfaces/ISelfPermit.sol#L40

https://github.com/code-423n4/2022-12-Stealth-Project/blob/2c798a70333c83102affd19c4346412be4bf859e/router-v1/contracts/interfaces/ISelfPermit.sol#L47


### Mitigation
Reduce line length to less than 120 at least to improve maintainability and readability of the code 

