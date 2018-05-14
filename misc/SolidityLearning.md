# Solidity Learning
All tips and info that I learn while going through Solidity. 

## About

Solidity is a language used to create Smart Contracts on the Ethereum blockchain (or blockchains that use the EVM). 
The code, although similar to javascript, has many select methods for transferring currency, checking ownership and
having access control. However, at this time it is still quite new and requires careful thought as bugs may prove fatal
to the execution of the smart contract.

### Helpful tools/references

* [https://cryptozombies.io](https://cryptozombies.io) - Solidity Tutorials.
* [https://remix.ethereum.org/](https://remix.ethereum.org/) - Solidity IDE in browser.
* [https://ethfiddle.com/](https://ethfiddle.com/) - Another Solidity IDE in browser.
* [Ethernaut](https://github.com/OpenZeppelin/ethernaut) - Ethereum Wargames (Vulnerability tutorials)
* [SafeMath](https://github.com/OpenZeppelin/zeppelin-solidity/blob/master/contracts/math/SafeMath.sol) - Safe math operations
* **ERC20**:
  * [EIP20](https://github.com/ethereum/eips/issues/20) - Etheruem Improvement Proposal for Token Standards.
  * [ERC20 Git](https://github.com/OpenZeppelin/zeppelin-solidity/tree/master/contracts/token/ERC20) - Implementation of ERC20 (EIP20).
* [``Web3.js``](https://github.com/ethereum/web3.js/)
  * [Docs 0.X.X](https://github.com/ethereum/wiki/wiki/JavaScript-API)
  * [Docs 1.X](http://web3js.readthedocs.io/en/1.0/index.html)

## Conventions and Code Style

* Function parameters start with `_`, e.g. ``function eatMe(string _name, uint _age)``
* Creating variables standalone (e.g. uint x;) is not good practice, instead just assign when
used (unless creating as an assigned value)
* Functions are **public** by default!
* Private functions start with an `_`
* Events usually declared at the top of the contract
* Mappings also need to be declared `public` if they are public. (e.g. ``mapping(address => uint) public people``)
* Use `require` to check conditions are true to execute certain functions.
* Comments are `//` for single line and ``/* ... */`` for multi-line.


## Misc

* A *view* function is when viewing data but not modifying.
* A *pure* function means you're not accessing anything in the app (just using function e.g `multiply` functions).
* Note: no secure random number generation! 
* Events are a way to communicate that something on the blockchain has occurred.
* There will always be a ``msg.sender`` for any given contract function call.
* **Inheritance**: a contract gets the functions from the contract that it inherits.
  * ``contract ExampleA is ExampleB``
  * ``contract ExampleA is ExampleB, ERC721``
* Memory and Storage - can use with streucts and arrays within functions.
  * `Storage` is **permanent** memory (stored on chain).
    * Note: `storage` is costly.
  * `Memory` is **temporary** memory erased between external function calls of the contract.
    * Cheaper (in some cases) to rebuild in memory.
    * Note: `arrays` must have full size for building in memory: ``uint[] foo = new uint[](3);``
* Solidity function types:
  * `public` - accessed by all
  * `private` - accessed only internally
  * `internal` - same as private but accessible by contracts that inherit from contract
  * `external` - similar to public but only called *outside* the contract (Can't be called by other functions inside)
* Can use an interface to make a contract interface to already deployed contracts.
* Comparing strings - it is better to compare the `keccak256` or `sha3` hashes than the strings.
* Function `modifier`
  * ``modifier modifierName()`` creates a function-like statement that can be attached to other functions.
  * ``function helloWorld() modifierName`` - will call the modifier with the function. Usually used for access control.
  * *Note:* modifiers can also take arguments.
* `uint` sizes aren't what they seem. Solidity automatically reserves 256 bits of storage for `uint8` or `unit256 (uint)`.
  * **Struct Packing**: In a `struct` that is different. Saving sizes of `uint` actually affects the size of the struct. 
  * Solidity works in 256 bits, e.g. if you do `uint128,unint128` they can fit in the same spot, if you do arithmetic with `uint16` it will cost more gas.
* Time in solidity:
  * `now` - returns the uint256 timestamp of the current time.
  * Also stores time intervals `seconds`, `minutes`, `hours`, `days`, `weeks` and `years`.
  * Can do math ``now + 5  minutes`` etc.
* Passing structs: 
  * Able to pass to `private` or `internal` functions.
  * `storage` pointer to a struct as an argument. (``function _test(StructName storage _structinstance) internal``).
* `payable` - allows for functions to receive payment (adds ``msg.value``).
  * If the function is not `payable`, a transaction that sends ether will be *rejected*.
* Monetary value can be coded in Solidity:
  * `ether` - the ether value. (e.g. ``0.001 ether``)
  * `wei` - the `wei` value.
  * `gwei` - the `gwei` value.
* Transferring money: ``owner.transfer(this.balance);`` - transfers the balance of the contract to the owner.
  * Transfer Logic (including tokens):
* [SafeMath](https://github.com/OpenZeppelin/zeppelin-solidity/blob/master/contracts/math/SafeMath.sol) library by **OpenZepplin** provides safe maths.
  * Use the `safemath` syntax to ensure correct execution.
* **Library**:
  * Special solidity contract.
  * Attach functions to native data types.
  * ``library myLibraryName``
  * e.g. (Safemath add) ``function add(uint256 a, uint256b) internal pure returns (uint256)``
  * How to use?
    1. ``import "./safemath.sol"``
    2. ``using SafeMath for uint256``

## Tokens

* Tokens are just smart contracts that follow common rules.
  * Example: `Transfer`, `BalanceOf` and a `mapping` that keeps track of how much balance you have.
  * The **ERC20** token standard means those tokens have the same set of functions (with same name).
  * An application that interacts with the `ERC20` token standard is also capable of adding more tokens in the future.
* Other token standards exist, all with differing interfaces and meaning.
* ERC721 tokens
  * Non-fungible / unique tokens on the blockchain.
  * Almost like 'collectible/one of a kind'.
* Two ways to transfer tokens:
  * ``transfer(address _to, uint256 _tokenId)`` - token owner calls transfer;
  * ``approve(address _to, uint256 _tokenId)`` - token owner calls approve and then transfer; contract stores who is approved;
  * ``takeOwnership(uint256 _tokenId)`` - only approved will be able to take ownership of a token;

## Contract Commenting

Follow [``natspec``](https://ethereum.gitbooks.io/frontier-guide/content/natspec.html) for coding guidelines and comments.

* **Title**:  ``/// @title Title of the Contract.``
* **Author**: ``/// @author The author of the contract.``
* **Developer Comments**: ``/// @dev explains what each function does.``
* **User Notice**: ``/// @notice explains to the user what the contract function does.``


## Interacting with Contracts

* Making web applications interact with contracts using [``web3.js``](https://github.com/ethereum/web3.js/)
  * Note: be careful of version changes and API changes. They're usually quick and sometimes undocumented.
  * Follow the guidelines at:
    * [Documentation  0.X.X](https://github.com/ethereum/wiki/wiki/JavaScript-API)
    * [Documentation 1.X](http://web3js.readthedocs.io/en/1.0/index.html)
* [Metamask](http://web3js.readthedocs.io/en/1.0/index.html) - Provides a chrome extension as a wallet!
* [Infura](https://infura.io/) - Provides an ethereum provider to send your web3 to!
  * This means you don't have to run a local node.
  * However, be aware that they may slow down or add different fees. There are positive's and negatives of running your own nodes.

## Example Contracts:

```solidity
pragma solidity ^0.4.19;

import "./myOtherContract.sol";

contract ExampleContract {
    uint testUint = 0;

    mapping (uint => address) personId;

    modifier onlyOwner(uint _exampleId) {
      // Note the end _ is important!
      require(personId[_exampleId] == msg.sender);
      _;
    }

    function checkMate(uint _exampleId) external view onlyOwner returns (bool) {
        return true;
    }
}
```
