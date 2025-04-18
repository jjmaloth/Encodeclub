Lesson 6 - Building unit tests for HelloWorld.sol
(Review) Coding in VS Code

    Syntax for typescript scripts
    How the project operates
    Writing a test file
    Using Viem library
    Using Hardhat toolbox
    Testing syntax
    Running a test file

References

https://viem.sh/

https://hardhat.org/hardhat-runner/docs/getting-started#overview

https://hardhat.org/hardhat-runner/docs/advanced/using-viem

https://mochajs.org/

https://hardhat.org/hardhat-chai-matchers/docs/overview

https://www.chaijs.com/guide/
TDD methodology

    Writing unit tests that fail first
    Filling code that completes the test
    Refactoring
    Measuring code smell
    Measuring coverage
    Iteration process
    How HelloWorld.sol could be tested

References for TDD

https://www.agilealliance.org/glossary/tdd/

https://www.browserstack.com/guide/what-is-test-driven-development
Running tests on HelloWorld.sol

    Contract

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

    Tests

// https://hardhat.org/hardhat-runner/docs/advanced/hardhat-runtime-environment
// https://hardhat.org/hardhat-runner/docs/advanced/using-viem
import { viem } from "hardhat";
// https://www.chaijs.com/guide/styles/#expect
import { expect } from "chai";
// https://hardhat.org/hardhat-network-helpers/docs/overview
// https://hardhat.org/hardhat-network-helpers/docs/reference#fixtures
import { loadFixture } from "@nomicfoundation/hardhat-toolbox-viem/network-helpers";

// https://mochajs.org/#getting-started
describe("HelloWorld", function () {
  async function deployContractFixture() {
    // https://hardhat.org/hardhat-runner/docs/advanced/using-viem#clients
    const publicClient = await viem.getPublicClient();
    const [owner, otherAccount] = await viem.getWalletClients();
    // https://hardhat.org/hardhat-runner/docs/advanced/using-viem#contracts
    const helloWorldContract = await viem.deployContract("HelloWorld");
    // https://www.typescriptlang.org/docs/handbook/2/functions.html#parameter-destructuring
    return {
      publicClient,
      owner,
      otherAccount,
      helloWorldContract,
    };
  }

  it("Should give a Hello World", async function () {
    const { helloWorldContract } = await loadFixture(deployContractFixture);
    // https://hardhat.org/hardhat-runner/plugins/nomicfoundation-hardhat-viem#contracts
    const helloWorldText = await helloWorldContract.read.helloWorld();
    // https://www.chaijs.com/api/bdd/#method_equal
    expect(helloWorldText).to.equal("Hello World");
  });

  it("Should set owner to deployer account", async function () {
    const { helloWorldContract, owner } = await loadFixture(
      deployContractFixture
    );
    // https://hardhat.org/hardhat-runner/plugins/nomicfoundation-hardhat-viem#contracts
    const contractOwner = await helloWorldContract.read.owner();
    // https://www.chaijs.com/api/bdd/#method_equal
    expect(contractOwner.toLowerCase()).to.equal(owner.account.address);
  });

  it("Should not allow anyone other than owner to call transferOwnership", async function () {
    const { helloWorldContract, otherAccount } = await loadFixture(
      deployContractFixture
    );
    // https://hardhat.org/hardhat-runner/plugins/nomicfoundation-hardhat-viem#retrieving-an-existing-contract
    const helloWorldContractAsOtherAccount = await viem.getContractAt(
      "HelloWorld",
      helloWorldContract.address,
      { client: { wallet: otherAccount } }
    );
    // https://www.chaijs.com/plugins/chai-as-promised/
    await expect(
      helloWorldContractAsOtherAccount.write.transferOwnership([
        otherAccount.account.address,
      ])
    ).to.be.rejectedWith("Caller is not the owner");
  });

  it("Should execute transferOwnership correctly", async function () {
    const { publicClient, helloWorldContract, owner, otherAccount } =
      await loadFixture(deployContractFixture);
    // https://hardhat.org/hardhat-runner/plugins/nomicfoundation-hardhat-viem#contracts
    const txHash = await helloWorldContract.write.transferOwnership([
      otherAccount.account.address,
    ]);
    // Transactions are instantly mined in the local network, but it is important to remember to await for confirmations when using a public network
    // https://viem.sh/docs/actions/public/getTransactionReceipt#gettransactionreceipt
    const receipt = await publicClient.getTransactionReceipt({ hash: txHash });
    // https://viem.sh/docs/glossary/terms#transaction-receipt
    expect(receipt.status).to.equal("success");
    const contractOwner = await helloWorldContract.read.owner();
    expect(contractOwner.toLowerCase()).to.equal(otherAccount.account.address);
    // It is important to check all relevant indirect effects in your tests
    const helloWorldContractAsPreviousAccount = await viem.getContractAt(
      "HelloWorld",
      helloWorldContract.address,
      { client: { wallet: owner } }
    );
    await expect(
      helloWorldContractAsPreviousAccount.write.transferOwnership([
        owner.account.address,
      ])
    ).to.be.rejectedWith("Caller is not the owner");
  });

  it("Should not allow anyone other than owner to change text", async function () {
    // TODO
    throw Error("Not implemented");
  });

  it("Should change text correctly", async function () {
    // TODO
    throw Error("Not implemented");
  });
});

Homework

    Create Github Issues with your questions about this lesson
    Read the references

