# Smart Contract Lottery (Raffle) — Chainlink VRF

A decentralized, provably fair raffle contract built with Foundry. Uses Chainlink VRF 
for verifiable randomness and Chainlink Automation to trigger the draw automatically 
on a time interval, without any centralized party picking the winner.

## How it works
- Users enter the raffle by sending ETH via `enterRaffle()`
- After a set time interval, Chainlink Automation triggers `performUpkeep`
- This requests a random number from Chainlink VRF
- The VRF callback (`fulfillRandomWords`) picks and pays the winner

## Tech stack
- Solidity ^0.8.x
- Foundry (Forge, Anvil, Cast)
- Chainlink VRF v2 (mocked locally, live on Sepolia)
- Chainlink Automation

## Setup
```shell
git clone --recurse-submodules https://github.com/Zaibiii12/smart-contract-lottery.git
cd smart-contract-lottery
forge install
forge build
forge test
```

## Deploy (Sepolia)
```shell
forge script script/DeployRaffle.s.sol --rpc-url $SEPOLIA_RPC_URL --account <your-account> --broadcast
```

## What I learned / issues solved
- Chainlink restructured their contract repo mid-course; had to remap imports 
  from the old `interfaces/` path to the new `shared/interfaces/` path.
- [add more as you go — e.g. VRF mock setup, gas optimization notes, testing approach]

## Status
🚧 In progress — currently implementing VRF mock deployment and Automation integration.