# ETH FUND ME

ETH Fund Me is a fundraising smart contract where a beneficiary can engage the service (e.g., this smart contract) to raise money in ethers for up to a certain amount specified by the beneficiary. The fundraising cannot start until a beneficiary is selected by paying a commitment fee greater than 10% of the amount to be raised. Only one fundraising can take place at a time. The fundraising will continue until the amount raised is met or exceeded, the fundraising time limit has been exceeded, or until the smart contract is suspended or end by the owner.

For a video demonstration of the DApp, refer to the video presentation shown below:

[download video here](https://1drv.ms/p/s!Athwhc7bxjKBhZMMc7n27yt0hsVL6Q?e=S37rkb)

The video file may take a bit of time to download (e.g. over 552 MB) and you need Powerpoint 2016 or later.

![ETHFUNDME](/docs/img/EthFundMe.PNG)

## Design & Structure

### Design Patterns

[Follow this link](/docs/design_pattern_decisions.md) for more information on the design patterns used in this project.

### Security Considerations

As a security engineer, I took extra care in insuring that the DApp is designed correctly and evaluated to be free from high and critical vulnerabilities.
[Follow this link](/docs/avoiding_common_attacks.md) for more information on how the DApps avoids common attacks.

### Extending Contracts

The structure of this decentralized smart contract uses two different smart contracts. The [Donations](/contracts/Donations.sol) contract contains the actual funding functionality. The Donations contract inherits payable beneficiary and owner addresses and setup for the contract from the [FromParent](/contracts/FromParent.sol).

### Usage of Library

The contract also incorporates SafeMath from OpenZeppelin as library for mathematical operations including Add, Sub, and Div to safeguard against overflows and underflows.

## The Process

ETH Fund Me is a fundraising smart contract where a beneficiary can engage the service (e.g., this smart contract) to raise money in ethers for up to a certain amount specified by the beneficiary. The fundraising cannot start until a beneficiary is selected by paying a commitment fee greater than 10% of the amount to be raised. Only one fundraising can take place at a time. The fundraising will continue until:

    i) the amount raised is met or exceeded;
    ii) the fundraising time limit has been exceeded;
    iii) the smart contract is suspended or end by the owner.

The ETH Fund Me process works as follow:

1. A beneficiary must specify the amount to raise.
2. A beneficiary must escrow a commitment fee of greater than 10% of the amount to be raised in order to be accepted by the smart contract to start the fundraising process. If accepted, the beneficiary address and the donation limit should be displayed. The fundraising is valid for only 7 days, or at least 604,800 seconds from accepted time.
3. One of more donors can donate to the contract for the beneficiary. There are no restrictions on the donation amounts except that a beneficiary must have been committed (e.g., selected) and the donor cannot be the contract owner or beneficiary.
4. Donations can be collected until the Amount to Raise has been met, or when the fundraising time limit has exceeded, at which time it is then automatically settled.
5. In the settlement, the contract owner gets 10% of the total donations collected and the beneficiary gets the remaining 90%.

## Local Installation

### Prerequisites

We need Node.js as the core, and Truffle to compile, migrate and test the smart contracts. We also need Ganache-cli to run a local development blockchain instance. Finally, we also need browsersync and lite server. We assume that things like git, npm, etc. are already installed as well.

Metamask (🦊) also needs to be installed to interact with the application inside a browser. Metamask should also be setup to connect to the local blockchain at HTTP://127.0.0.1:8545 using the built-in RPC.

### Run the Prerequisites

With the prerequisites installed, we need to run a local Ganache-cli instance. To do this, open two different terminal windows.
In the first window, run Ganache-cli and wait for it to be started. Copy over the mnemonic, as this needs to be entered into Metamask for local testing. Please note that Metamask may require a reset of the account connected to the local blockchain after the setup to insure the nonce is sync to local blockchain. This is needed to ensure a clean network state - [https://metamask.zendesk.com/hc/en-us/articles/360015488891-Resetting-an-Account]

```
ganache-cli
```

Ganache-cli needs to keep running in the background, while the second terminal window will be used for running the tests / application. You may also need to import the private keys for the test accounts generated by Ganache-cli. You will need at least 3 test accounts (e.g., owner, beneficiary, and donor) to experiment with the dApp.

### Compilation & Migration

The project including all dependencies can be set up using npm.

```
npm install
```

All contracts can be compiled with Truffle.

```
truffle compile
```

The contracts can be migrated to the running ganache-cli instance with Truffle. Use reset to clear any prior state variables.

```
truffle migrate --reset
```

Optionally, the contracts can also be migrated to the Ropsten test network.
Before doing so, however, the following line should be updated in the truffle-config.js with your actual seed words (your mneumonic) from your Metamask wallet with Ropsten accounts:

```
const MNEMONIC = 'YOUR WALLET KEY';
```

In order to migrate to the Ropsten testnet, you must also update your Infura project ID or you can just use my ID as shown.

```
return new HDWalletProvider(MNEMONIC, "https://ropsten.infura.io/v3/e3cad676e53b4505b845d1208c6f61c2")
```

Once the above two changes have been made in the truffle-config.js, you can then migrate the project to Ropsten by:

```
truffle migrate --network ropsten
```

For your convenience, I have also deployed my final version of the contracts at address 0xfdf087e45281Db3067B7933dF41849F746906BC4 of the Ropsten network. The corresponding address can be found [here](/deployed_addresses.txt). You should connect the front-end only after you have successfully migrated the project.

On the Ropsten network, the state variables of the front-end update sporadically (may require a minute or two since Ropsten uses Proof of Work). Accordingly, you will need to wait more time for LOADING to complete. ![Ropsten Confirmation](/docs/img/Ropsten1.PNG) ![Ropsten Loading](/docs/img/Ropsten_Loading.PNG)|

Occasionally, you may need to force browser refresh yourself if you are impatient like me. However, the dApp should work normally for the Ganache-cli local blockchain and Ropsten.

For free test ethers for the Ropsten, you can go to https://teth.bitaps.com/ and get an ether every minute!

### Unit tests

The tests can be executed with truffle using the command as shown.

```
truffle test
```

![Tests](/docs/img/testsRan.png)|

A total of six formal tests were built using Javascript and to provide complete testing coverage of contract functions including:

a. Checking that the constructor is setting the owner as the deployer of the contract. This test uses the account of the contract to check if the owner account captured during initialization is the same.

b. Set beneficiary and related conditions are met. This portion performs two tests. First, it uses a second account and sends to the contract the minimum amount requires to establish the donation limit. Secondly, it also tests to ensure that the escrow payment cannot be the same or greater than the donation limit.

c. Any donor can donate and up to the donation limit or higher. This portion also performs two tests. First, it checks that the donor cannot be the contract owner or the beneficiary. Secondly, it ensures that the donor can send in any donor amount.

d. Transfer is made when donation limit has been met or exceeded. This test checks the proper transfer calculation is performed and made in the settlement to the owner and beneficiary accounts when the aggregated donations have equaled or exceeded the donation limit.

#### Testing Fundraising Time Expiration

In the constructor of the Donations contract, you can alter the duration variable ![Fundraising Time Limit](/docs/img/Duration.PNG) from 604,800 seconds (7 days) to 60 or 120 seconds for testing. You will need to compile and redeploy to the appropriate network to check this behavior. You can confirm that time has expired when the Beneficiary address has reset to 0x0 and settlement automatically took place.

### Running the Front-end

Once the contracts have been succesfully migrated to either the local blockchain using Ganache-cli or onto Ropsten, the front-end can then be run.

```
npm run dev
```

The front-end can then be accessed at `http://localhost:3000/`. Please note that the actual port may be incremented by one if you have already actively deployed onto the port.

#### Testing using Ganache

If you are connecting with Ganache instead of Ganache-cli, be sure to perform the following:

    1. Modify the truffle-config.js file to use 'port: 7545'.
       You will need to compile and redeploy after you have Ganache up and running.

    2. Import the Ganache accounts into MetaMask using the generated mnemonic.
       Please note that with Ganache, Metamask may require a reset of the acccount connected
       to the local blockchain after the setup to insure the nonce is properly synced to the
       local blockchain. This is needed to ensure a clean network state
       https://metamask.zendesk.com/hc/en-us/articles/360015488891-Resetting-an-Account

## Application Usage

The application has three sections:

a. Beneficiary Section - Allow for any valid users to transact with the contract and nominate self as the beneficiary for his/her own fundraising. To be the beneficiary, the user must submit an donation limit to be raised and an escrow payment (e.g., a commitment fee) of greater than 10% of the donation limit. The escrow payment is needed to prevent spamming of contract. When beneficiary has been accepted by the contract, the beneficiary address should be displayed along with the contract balance. When no beneficiary has been selected, the beneficiary address should be zero. The Book_My_Fundraising button sends the entered donation limit and escrow payment to Metamask for confirmation and then to the contract.

b. Donor Section - Allow for any user except the beneficiary and the owner to donate to the contract on the behalf of the beneficiary. Donation cannot be made unless a beneficiary has been selected. Donations collected are displayed in both wei and ethers along with the donation limit to be raised. The Submit_Donation button sends the donation to Metamask for confirmation and then to the contract.

c. Owner Section - Is designed to be accessible only by the contract owner with the address shown determined when the contract is first initialized. By default, both emergency circuit breaker and transfer unlock are set to Not Active. The End Contract button allows for the contract to be terminated and any remaining funds are sent to the contract owner.

On the initial launch, Metamask will be invoked (and if not already login), the user must consent to use with the dApp.

For more help on testing, refer to the video presentation shown [here](https://1drv.ms/p/s!Athwhc7bxjKBhZMMc7n27yt0hsVL6Q?e=S37rkb). The video file may take a bit of time to download (e.g. over 552 MB) and you need Powerpoint 2016 or later.

## Thank You for Your Time in the Evaluation of my ETH FUND ME dApp.

## Please give me all TOP scores and connect with me on Linkedin!

![Thank You Very Much](/docs/img/168956283.jpg)
