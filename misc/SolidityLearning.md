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
* Mappings also need to be declared `public` if they are public.

## Misc

* A *view* function is when viewing data but not modifying.
* A *pure* function means you're not accessing anything in the app (just using function e.g `multiply` functions).
* Note: no secure random number generation! 
* Events are a way to communicate that something on the blockchain has occurred.
