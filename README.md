# Functions-and-Errors---ETH-AVAX
# ErrorHandling Smart Contract

This project demonstrates the use of `require()`, `assert()`, and `revert()` statements in a Solidity smart contract. The contract is designed to be deployed on both the Ethereum (ETH) and Avalanche (AVAX) networks.

## Contract Overview

The `ErrorHandling` contract includes the following functionalities:
- Setting a value with a condition (`setValue`)
- Checking if the caller is the owner (`checkOwner`)
- Dividing two numbers with error handling (`divide` and `safeDivide`)
- Updating the owner's address with a check (`updateOwner`)

## Features

- **require()**: Ensures conditions are met before executing further logic.
- **assert()**: Verifies assumptions made by the contract.
- **revert()**: Reverts the transaction if an error condition is met.

## Prerequisites

To work with this project, you need:

- [Remix IDE](https://remix.ethereum.org/)
- [Node.js](https://nodejs.org/) and [npm](https://www.npmjs.com/) (for testing with Hardhat)

## Getting Started

### Step 1: Open Remix IDE

1. Open your web browser and navigate to [Remix IDE](https://remix.ethereum.org/).

### Step 2: Create a New File

1. In the File Explorer panel on the left, click on the "contracts" folder.
2. Click the "+" button to create a new file.
3. Name your new file `ErrorHandling.sol`.

### Step 3: Write the Smart Contract

1. Copy and paste the following Solidity code into `ErrorHandling.sol`:

    ```solidity
    // SPDX-License-Identifier: MIT
    pragma solidity ^0.8.0;

    contract ErrorHandling {
        address public owner;
        uint256 public value;

        constructor() {
            owner = msg.sender;
        }

        // Function to set a value, demonstrating require()
        function setValue(uint256 _value) public {
            require(_value > 0, "Value must be greater than zero");
            value = _value;
        }

        // Function to assert the owner's address
        function checkOwner() public view {
            assert(owner == msg.sender);
        }

        // Function to demonstrate revert()
        function divide(uint256 _numerator, uint256 _denominator) public pure returns (uint256) {
            if (_denominator == 0) {
                revert("Denominator cannot be zero");
            }
            return _numerator / _denominator;
        }

        // Function to demonstrate multiple checks and revert() with a custom error
        function safeDivide(uint256 _numerator, uint256 _denominator) public pure returns (uint256) {
            if (_denominator == 0) {
                revert("Cannot divide by zero");
            }
            uint256 result = _numerator / _denominator;
            require(result >= 0, "Result must be non-negative");
            return result;
        }

        // Function to update the owner's address, demonstrating onlyOwner modifier and assert
        function updateOwner(address _newOwner) public onlyOwner {
            owner = _newOwner;
            assert(owner == _newOwner); // Check if the update was successful
        }

        // Modifier to check if the caller is the owner
        modifier onlyOwner() {
            require(msg.sender == owner, "Caller is not the owner");
            _;
        }
    }
    ```

### Step 4: Compile the Smart Contract

1. Click on the "Solidity Compiler" tab on the left sidebar (it looks like a solid square).
2. Make sure the compiler version is set to at least `0.8.0`.
3. Click the "Compile ErrorHandling.sol" button.

### Step 5: Deploy the Smart Contract

1. Click on the "Deploy & Run Transactions" tab on the left sidebar (it looks like an Ethereum logo).
2. Ensure the "Environment" is set to "JavaScript VM" for testing purposes.
3. Click the "Deploy" button under "Deploy & Run Transactions".

### Step 6: Interact with the Smart Contract

1. After deployment, the contract will appear in the "Deployed Contracts" section.
2. Expand the contract to see its functions and interact with them.

## Testing with Hardhat

To test the contract using Hardhat, follow these steps:

### Step 1: Set Up Hardhat

1. Initialize a new project directory and run:

    ```bash
    npm init -y
    npm install --save-dev hardhat
    npx hardhat
    ```

2. Follow the prompts to create a basic sample project.

### Step 2: Write Tests

1. Create a new file in the `test` directory, e.g., `ErrorHandling.js`.
2. Copy and paste the following test code into this file:

    ```javascript
    const { expect } = require("chai");

    describe("ErrorHandling Contract", function () {
        let ErrorHandling, errorHandling, owner, addr1;

        beforeEach(async function () {
            ErrorHandling = await ethers.getContractFactory("ErrorHandling");
            [owner, addr1] = await ethers.getSigners();
            errorHandling = await ErrorHandling.deploy();
            await errorHandling.deployed();
        });

        it("Should set value correctly", async function () {
            await errorHandling.setValue(42);
            expect(await errorHandling.value()).to.equal(42);
        });

        it("Should revert if value is zero", async function () {
            await expect(errorHandling.setValue(0)).to.be.revertedWith("Value must be greater than zero");
        });

        it("Should assert owner", async function () {
            await errorHandling.checkOwner();
        });

        it("Should revert if not owner", async function () {
            await expect(errorHandling.connect(addr1).checkOwner()).to.be.reverted;
        });

        it("Should divide correctly", async function () {
            expect(await errorHandling.divide(10, 2)).to.equal(5);
        });

        it("Should revert on divide by zero", async function () {
            await expect(errorHandling.divide(10, 0)).to.be.revertedWith("Denominator cannot be zero");
        });

        it("Should update owner", async function () {
            await errorHandling.updateOwner(addr1.address);
            expect(await errorHandling.owner()).to.equal(addr1.address);
        });

        it("Should revert if caller is not owner", async function () {
            await expect(errorHandling.connect(addr1).updateOwner(addr1.address)).to.be.revertedWith("Caller is not the owner");
        });
    });
    ```

### Step 3: Run Tests

1. Run the tests using:

    ```bash
    npx hardhat test
    ```

This will execute the tests and display the results, ensuring your contract works as expected.

## License

This project is licensed under the MIT License. See the [LICENSE](LICENSE) file for details.
