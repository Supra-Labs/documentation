---
description: Initialize a new Hardhat project and prepare it for deployment on SupraEVM.
---

# Start Building

{% stepper %}
{% step %}
### <mark style="color:red;">Step 1: Initialize Your Project</mark>

Set up your folder, initialize Hardhat, and prepare your config file.

```
mkdir SupDAO
cd SupDAO
npx hardhat init
```

Choose the **empty `hardhat.config.js` template** and install any required node modules.
{% endstep %}

{% step %}
### <mark style="color:red;">Step 2: Configure Hardhat for SupraEVM</mark>

Update your `hardhat.config.js` file with the SupraEVM network:

{% hint style="info" %}
For the latest RPC URL, please refer to the [Supra Network Information](../../network-information.md) page.
{% endhint %}

```
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
### <mark style="color:red;">Step 3: Create Your DAO Contract</mark>

Inside a new `contracts/` directory, create your Solidity file (e.g., `CrossChainDAO.sol`):

```
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
### <mark style="color:red;">Step 4: Compile Your Contract</mark>

Make sure the Solidity code builds successfully.

```
npx hardhat compile
```
{% endstep %}

{% step %}
### <mark style="color:red;">Step 5: Create a Deployment Script</mark>

Add a `scripts/deploy.js` file to deploy your DAO contract to SupraEVM.

```
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
{% endstep %}

{% step %}
### <mark style="color:red;">Step 6: Deploy to SupraEVM</mark>

Run your deploy script on the SupraEVM network.

```
npx hardhat run scripts/deploy.js --network supra
```
{% endstep %}

{% step %}
### <mark style="color:red;">Step 7: Create Interaction Script</mark>

Add a menu-based script that interacts with your deployed DAO.

```
const { ethers } = require("hardhat");

async function main() {
    const daoAddress = ""; // Replace with your deployed DAO contract address
    const abi = []; // Add ABI after deployment
    const signer = await ethers.getSigner();
    const daoContract = new ethers.Contract(daoAddress, abi, signer);

    const readline = require("readline").createInterface({
        input: process.stdin,
        output: process.stdout,
    });

    async function menu() {
        console.log("\nWelcome to the DAO Interaction Script!");
        console.log("1. Add a DAO Member");
        console.log("2. Create a Proposal");
        console.log("3. Vote on a Proposal");
        console.log("4. Execute a Proposal");
        console.log("5. Exit");

        readline.question("Enter your choice: ", async (action) => {
            try {
                if (action === "1") {
                    readline.question("Member address: ", async (address) => {
                        const tx = await daoContract.addMember(address);
                        await tx.wait();
                        console.log(`Member added: ${address}`);
                        await menu();
                    });
                } else if (action === "2") {
                    readline.question("Proposal description: ", async (description) => {
                        const tx = await daoContract.createProposal(description);
                        await tx.wait();
                        console.log("Proposal created!");
                        await menu();
                    });
                } else if (action === "3") {
                    readline.question("Proposal ID: ", async (id) => {
                        const tx = await daoContract.vote(id);
                        await tx.wait();
                        console.log(`Voted on proposal ${id}`);
                        await menu();
                    });
                } else if (action === "4") {
                    readline.question("Proposal ID: ", async (id) => {
                        const tx = await daoContract.executeProposal(id);
                        await tx.wait();
                        console.log(`Executed proposal ${id}`);
                        await menu();
                    });
                } else if (action === "5") {
                    console.log("Exiting...");
                    readline.close();
                } else {
                    console.log("Invalid choice.");
                    await menu();
                }
            } catch (err) {
                console.error("Error:", err.message);
                await menu();
            }
        });
    }

    await menu();
}

main().catch((error) => {
    console.error(error);
    process.exitCode = 1;
});
```
{% endstep %}

{% step %}
### <mark style="color:red;">Step 8: Run the Interaction Script</mark>

Run your interaction script:

```
npx hardhat run scripts/interact.js --network supra
```
{% endstep %}
{% endstepper %}
