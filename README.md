# Decentralized Donation Platform

## Overview

This decentralized donation platform allows users to contribute Ether directly to the smart contract. Donations are tracked on the Ethereum blockchain, and only the contract owner can withdraw the collected funds. The platform ensures transparency and accountability by recording every donation.

## Features

- **Donation Tracking**: Records each donation with donor address, amount, and timestamp.
- **Event Logging**: Emits an event for each donation received.
- **Owner Withdrawal**: Allows only the contract owner to withdraw the funds collected.

## Smart Contract

### DonationPlatform Contract
1. Solidity Smart Contract
   
```solidity
// SPDX-License-Identifier: MIT
pragma solidity ^0.8.0;

contract DonationPlatform {
    struct Donor {
        address donorAddress;
        uint256 amount;
        uint256 timestamp;
    }
    
    mapping(uint256 => Donor) public donations;
    uint256 public donationCount;
    address public owner;
    event DonationReceived(address indexed donor, uint256 amount, uint256 donationId);
    
    constructor() {
        owner = msg.sender;
        donationCount = 0;
    }
    
    function donate() public payable {
        require(msg.value > 0, "Donation must be greater than 0");
        donationCount++;
        donations[donationCount] = Donor(msg.sender, msg.value, block.timestamp);
        emit DonationReceived(msg.sender, msg.value, donationCount);
    }
    
    function withdraw() public {
        require(msg.sender == owner, "Only the owner can withdraw funds");
        payable(owner).transfer(address(this).balance);
    }
}
```
2. Frontend Development with React.js

2.1 App.js
```
 import React, { useState, useEffect } from 'react';
 import Web3 from 'web3';
 import DonationPlatform from './DonationPlatform.json';
 const App = () => {
  const [account, setAccount] = useState('');
  const [donationPlatform, setDonationPlatform] = useState(null);
  const [donationCount, setDonationCount] = useState(0);
  useEffect(() => {
    loadWeb3();
    loadBlockchainData();
  }, []);
  const loadWeb3 = async () => {
    if (window.ethereum) {
      window.web3 = new Web3(window.ethereum);
      await window.ethereum.enable();
    }
  };
  const loadBlockchainData = async () => {
    const web3 = window.web3;
    const accounts = await web3.eth.getAccounts();
    setAccount(accounts[0]);
    const networkId = await web3.eth.net.getId();
    const networkData = DonationPlatform.networks[networkId];
    if (networkData) {
      const platform = new web3.eth.Contract(
        DonationPlatform.abi,
        networkData.address
      );
      setDonationPlatform(platform);
      const count = await platform.methods.donationCount().call();
      setDonationCount(count);
    }
  };
  const donate = async (amount) => {
    await donationPlatform.methods.donate().send({ from: account, value: amount });
  };
  return (
    <div>
      <h1>Decentralized Donation Platform</h1>
      <p>Your Account: {account}</p>
      <button onClick={() => donate(window.web3.utils.toWei('0.1', 'Ether'))}>
        Donate 0.1 ETH
      </button>
      <p>Total Donations: {donationCount}</p>
    </div>
  );
 };
```
  2.2 web3.js
  
```
 import Web3 from 'web3';
 const getWeb3 = () =>
  new Promise((resolve, reject) => {
    window.addEventListener('load', async () => {
      if (window.ethereum) {
        const web3 = new Web3(window.ethereum);
        try {
          await window.ethereum.enable();
          resolve(web3);
        } catch (error) {
          reject(error);
        }
      } else if (window.web3) {
        resolve(window.web3);
      } else {
        reject('Non-Ethereum browser detected. You should consider trying MetaMask!');
      }
    });
  });
 export default getWeb3;
```



## Vision

The platform aims to provide a transparent and decentralized way for users to make donations directly on the Ethereum blockchain. It ensures that all donations are recorded and that funds can only be withdrawn by the contract owner, adding a layer of security and trust.

## Flowchart

1. **[Donate ETH]**:
   - Users send ETH to the contract.
2. **[Validate Donation]**:
   - The contract checks that the donation amount is greater than 0.
3. **[Record Donation]**:
   - The donation is recorded with donor address, amount, and timestamp.
4. **[Emit DonationReceived Event]**:
   - An event is emitted with the details of the donation.
5. **[Owner Withdrawal]**:
   - The owner can withdraw the total balance from the contract.

## Future Scope

- **User Interface**:
  - Develop a web interface to simplify the donation process and display donation history.
  
- **Enhanced Features**:
  - Implement features for different donation tiers or goals.
  - Integrate with additional blockchain networks for wider reach.

## Contract Address

- **DonationPlatform**: 0xd9145CCE52D386f254917e481eB44e9943F39138

## Contact

For questions or feedback, please reach out:

- **Name**: Bobby Singha
- **Email**: singhabobby2020@gmail.com
- **GitHub**: https://github.com/Bobby515-code

---
