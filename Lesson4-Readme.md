Lesson 4 - Interfaces and external calls
External calls

    What is a "MethodID"
    Fallback and receive functions
    External calls

External Call References

https://solidity-by-example.org/function-selector/

https://docs.soliditylang.org/en/latest/contracts.html#receive-ether-function

https://docs.soliditylang.org/en/latest/control-structures.html#external-function-calls
Using interfaces

    Fallback and receive functions
    External calls
    Attaching "mismatched" contracts
    Why some functions work and others don't because of the "MethodID"

Access Control References

https://docs.soliditylang.org/en/latest/common-patterns.html#restricting-access

https://docs.soliditylang.org/en/latest/units-and-global-variables.html#block-and-transaction-properties
Implementation Examples

// SPDX-License-Identifier: GPL-3.0
pragma solidity >=0.7.0 <0.9.0;

contract HelloWorld {
    string private text;
    address public owner;

    modifier onlyOwner()
    {
        require (msg.sender == owner, "Caller is not the owner");
        _;
    }

    constructor() {
        text = "Hello World";
        owner = msg.sender;
    }

    function helloWorld() public view returns (string memory) {
        return text;
    }

    function setText(string calldata newText) public onlyOwner {
        text = newText;
    }

    function transferOwnership(address newOwner) public onlyOwner {
        owner = newOwner;
    }
}

Contract interaction

    Interacting with previously deployed contracts using interfaces
    Inspecting "MethodId" mismatch and fallback functions
    Inspecting execution errors based on assertions
    Fixing the interfaces
    Interacting with other peoples contract

Extra reference material

    Handy reference sheet: Solidity Cheatsheet
    Overall style guide: Solidity Style Guide
    Solidity quick guide: Learn Solidity in Y minutes
    Typescript quick guide: Learn Typescript in Y minutes

Homework

    Create Github Issues with your questions about this lesson
    Read the references
    Prepare your environment for next week:
        Install Node v20 LTS
        NPM
        Yarn and/or Bun
        Git CLI
        VS Code
    Create a free account on Infura
    Create a free account on Alchemy
    Create a free account on Etherscan

Weekend Project

This is a group activity for at least 3 students:

    Interact with "HelloWorld.sol" within your group to change message strings and change owners
    Write a report with each function execution and the transaction hash, if successful, or the revert reason, if failed
    Submit your weekend project by filling the form provided in Discord

