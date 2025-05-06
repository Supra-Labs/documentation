---
description: Create and deploy your first Solidity contract on SupraEVM
---

# Your First Solidity Contract

## Project Overview

We will be building a Solidity-based DAO Management Contract on SupraEVM, this guide walks you through the complete process of developing, deploying, and interacting with your contact on SupraEVM Network using Hardhat.

## Prerequisites

* [Node.js](https://nodejs.org/en/download)
* [Hardhat](https://hardhat.org/)

## Start building

{% stepper %}
{% step %}
#### Initiate a new hardhat project

{% code overflow="wrap" %}
```bash
mkdir SupDAO
cd SupDAO
npx hardhat init
```
{% endcode %}

This will initiate your hardhat project templates interface. We'll use the `empty hardhat_config.js` template and follow the Instructions to install the supporting node modules.

{% embed url="https://files.gitbook.com/v0/b/gitbook-x-prod.appspot.com/o/spaces%2FkaUNeiOUvY0TI9c9oVPw%2Fuploads%2FUrd0nDiUQFWmr2b82yOC%2FScreen%20Recording%202025-04-11%20105718.mp4?alt=media&token=1a93ed66-c7ce-4419-bcc4-9ad11290a940" fullWidth="false" %}
Git Bash Terminal
{% endembed %}
{% endstep %}

{% step %}
#### Configure the `hardhat.config.js` file by adding the SupraEVM network configuration:

```javascript
module.exports = {
    solidity: "0.8.17",
    networks: {
        supra: {
            url: "SUPRAEVM_RPC_URL",
            accounts: ["YOUR_STARKEY_WALLET_PRIVATE_KEY"]
        }
    }
};
```
{% endstep %}

{% step %}
#### Create a new sub directory of `Contracts` and make a new Solidity contract within the directory.

This `.sol` contract will add the desired DAO functionalities to use in our Contract.

```solidity
// SPDX-License-Identifier: MIT
pragma solidity ^0.8.28;

contract CrossChainDAO {
    struct Proposal {
        uint id;
        string description;
        uint voteCount;
        bool executed;
    }

    address public owner;
    uint public proposalCount;
    mapping(uint => Proposal) public proposals;
    mapping(address => bool) public members;

    event ProposalCreated(uint id, string description);
    event VoteCasted(uint id, address voter);
    event ProposalExecuted(uint id);

    modifier onlyOwner() {
        require(msg.sender == owner, "Only owner can call this function");
        _;
    }

    modifier onlyMember() {
        require(members[msg.sender], "Only DAO members can vote");
        _;
    }

    constructor() {
        owner = msg.sender;
    }

    function addMember(address _member) external onlyOwner {
        members[_member] = true;
    }

    function createProposal(string memory _description) external onlyMember {
        proposalCount++;
        proposals[proposalCount] = Proposal(proposalCount, _description, 0, false);
        emit ProposalCreated(proposalCount, _description);
    }

    function vote(uint _id) external onlyMember {
        Proposal storage proposal = proposals[_id];
        require(!proposal.executed, "Proposal already executed");
        proposal.voteCount++;
        emit VoteCasted(_id, msg.sender);
    }

    function executeProposal(uint _id) external onlyOwner {
        Proposal storage proposal = proposals[_id];
        require(!proposal.executed, "Proposal already executed");
        require(proposal.voteCount > 0, "Insufficient votes to execute the proposal");
        proposal.executed = true;
        emit ProposalExecuted(_id);
    }
}
```
{% endstep %}

{% step %}
#### Compile your contract

{% code overflow="wrap" %}
```bash
npx hardhat compile
```
{% endcode %}
{% endstep %}

{% step %}
#### Setting the deployment script

Create a sub directory of scripts and make a deployment script within the `scripts/deploy.js` file

{% code overflow="wrap" %}
```javascript
const { ethers } = require("hardhat");

async function main() {
    const CrossChainDAO = await ethers.getContractFactory("CrossChainDAO");
    const dao = await CrossChainDAO.deploy();
    await dao.deployed();
    console.log("DAO deployed to:", dao.address);
}

main().catch((error) => {
    console.error(error);
    process.exitCode = 1;
});
```
{% endcode %}
{% endstep %}

{% step %}
#### Deploy the contract to SupraEVM

{% code overflow="wrap" %}
```bash
npx hardhat run scripts/deploy.js --network supra
```
{% endcode %}
{% endstep %}

{% step %}
#### Setting up the interaction script

Create an interactive script (`scripts/interact.js`) to perform all DAO functionalities.

{% code overflow="wrap" %}
```javascript
const { ethers } = require("hardhat");

async function main() {
    const daoAddress = " "; // Replace with your DAO contract address
    const abi = [
// add your contract ABI once you deploy it on SupraEVM
    ];
    const signer = await ethers.getSigner();
    const daoContract = new ethers.Contract(daoAddress, abi, signer);

    const readline = require("readline").createInterface({
        input: process.stdin,
        output: process.stdout,
    });

    async function menu() {
        console.log("\nWelcome to the DAO Interaction Script!");
        console.log("Choose an action:");
        console.log("1. Add a DAO Member");
        console.log("2. Create a Proposal");
        console.log("3. Vote on a Proposal");
        console.log("4. Execute a Proposal");
        console.log("5. Exit");

        readline.question("Enter the number of the action: ", async (action) => {
            try {
                if (action === "1") {
                    readline.question("Enter the member's address: ", async (address) => {
                        const tx = await daoContract.addMember(address);
                        await tx.wait();
                        console.log(`Address ${address} added as a DAO member!`);
                        await menu(); // Loop back to menu
                    });
                } else if (action === "2") {
                    readline.question("Enter the proposal description: ", async (description) => {
                        const tx = await daoContract.createProposal(description);
                        await tx.wait();
                        console.log("Proposal created successfully!");
                        await menu(); // Loop back to menu
                    });
                } else if (action === "3") {
                    readline.question("Enter the proposal ID to vote on: ", async (proposalId) => {
                        const tx = await daoContract.vote(proposalId);
                        await tx.wait();
                        console.log(`Vote cast for proposal ID ${proposalId}!`);
                        await menu(); // Loop back to menu
                    });
                } else if (action === "4") {
                    readline.question("Enter the proposal ID to execute: ", async (proposalId) => {
                        const tx = await daoContract.executeProposal(proposalId);
                        await tx.wait();
                        console.log(`Proposal ID ${proposalId} executed successfully!`);
                        await menu(); // Loop back to menu
                    });
                } else if (action === "5") {
                    console.log("Exiting. Goodbye!");
                    readline.close();
                } else {
                    console.log("Invalid action. Please try again.");
                    await menu(); // Loop back to menu
                }
            } catch (error) {
                console.error("An error occurred:", error.message);
                await menu(); // Loop back to menu
            }
        });
    }

    await menu(); // Start the menu loop
}

main().catch((error) => {
    console.error(error);
    process.exitCode = 1;
});
```
{% endcode %}
{% endstep %}

{% step %}
#### Interacting with the contract

Run the interactive script `scripts/interact.js` in your terminal.

```bash
npx hardhat run scripts/interact.js --network supra
```

{% embed url="https://files.gitbook.com/v0/b/gitbook-x-prod.appspot.com/o/spaces%2FkaUNeiOUvY0TI9c9oVPw%2Fuploads%2FGDolBehSGQiEm9iaz2Vr%2FScreen%20Recording%202025-04-11%20115026.mp4?alt=media&token=0214c695-c487-425a-a086-bff7cdac95e3" %}
{% endstep %}
{% endstepper %}

## Github repository for the contract breakdown and source code:

{% embed url="https://github.com/JatinSupra/SupDAO" %}
