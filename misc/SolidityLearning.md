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

## Conventions and Code Style

* Function parameters start with `_`, e.g. ``function eatMe(string _name, uint _age)``
* Creating variables standalone (e.g. uint x;) is not good practice, instead just assign when
used (unless creating as an assigned value)
* Functions are **public** by default!
* Private functions start with an `_`
* Events usually declared at the top of the contract
* Mappings also need to be declared `public` if they are public. (e.g. ``mapping(address => uint) public people``)
* Use `require` to check conditions are true to execute certain functions.

## Misc

* A *view* function is when viewing data but not modifying.
* A *pure* function means you're not accessing anything in the app (just using function e.g `multiply` functions).
* Note: no secure random number generation! 
* Events are a way to communicate that something on the blockchain has occurred.
* There will always be a ``msg.sender`` for any given contract function call.
* Memory and Storage - can use with streucts and arrays within functions.
  * `Storage` is **permanent** memory (stored on chain).
  * `Memory` is **temporary** memory erased between external function calls of the contract.
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
* Time in solidity:
  * `now` - returns the uint256 timestamp of the current time.
  * Also stores time intervals `seconds`, `minutes`, `hours`, `days`, `weeks` and `years`.
  * Can do math ``now + 5  minutes`` etc.
* Passing structs: 
  * Able to pass to `private` or `internal` functions.
  * `storage` pointer to a struct as an argument. (``function _test(StructName storage _structinstance) internal``).
  
