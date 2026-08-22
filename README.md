# Smart Contract Lottery (Raffle) — Chainlink VRF

A decentralized, provably fair raffle contract built with Foundry. Uses Chainlink VRF
for verifiable randomness and Chainlink Automation to trigger the draw automatically
on a time interval, without any centralized party picking the winner.

## How it works
- Users enter the raffle by sending ETH via `enterRaffle()` (must meet the minimum entrance fee)
- Chainlink Automation periodically checks `checkUpkeep()` to see if the raffle is ready to draw
  (enough time has passed, raffle is open, has a balance, and has players)
- When conditions are met, `performUpkeep()` requests a random number from Chainlink VRF
- Chainlink's VRF callback (`fulfillRandomWords`) uses that random number to pick a winner
  and automatically transfers the full contract balance to them

## Tech stack
- Solidity ^0.8.19
- Foundry (Forge, Anvil, Cast)
- Chainlink VRF v2.5 (mocked locally via `VRFCoordinatorV2_5Mock`, live on Sepolia)
- Chainlink Automation
- OpenZeppelin Contracts (transitive dependency via Chainlink's VRF mock)

## Project structure
```
src/
  Raffle.sol              # Main raffle contract
script/
  DeployRaffle.s.sol      # Deployment script
  HelperConfig.s.sol      # Network-specific config (Sepolia vs local Anvil)
test/                     # Tests (in progress)
```

## Setup
```shell
git clone --recurse-submodules https://github.com/Zaibiii12/smart-contract-lottery.git
cd smart-contract-lottery
forge install
forge build
forge test
```
> Note: this repo uses git submodules for its dependencies (Chainlink, OpenZeppelin, forge-std).
> If you already cloned it without `--recurse-submodules`, run:
> `git submodule update --init --recursive`

## Deploy (Sepolia)
```shell
forge script script/DeployRaffle.s.sol --rpc-url $SEPOLIA_RPC_URL --account <your-account> --broadcast
```

## What I learned / issues solved
- **Chainlink repo reorganization:** the course referenced an old Chainlink contracts path
  that no longer exists. Chainlink split their repo, moving `AggregatorV3Interface` (and others)
  to a new `shared/interfaces/` path under `smartcontractkit/chainlink-evm`. Fixed by installing
  the correct package and updating remappings.
- **Missing transitive dependency:** deploying `VRFCoordinatorV2_5Mock` pulled in Chainlink's
  internal `SubscriptionAPI.sol`, which itself depends on OpenZeppelin's `EnumerableSet.sol` —
  a dependency I hadn't installed directly. Traced this through the compiler's "unable to resolve
  imports" error and added OpenZeppelin as an explicit dependency with the correct remapping.
- **VRF v2.5 type mismatch:** the newer `VRFCoordinatorV2_5Mock` constructor expects `uint96` for
  base fee and gas price arguments, not `uint256` like older VRF mock versions. Fixed by correcting
  the type of `MOCK_BASE_FEE` and `MOCK_GAS_PRICE_LINK` in `HelperConfig.s.sol`.
- **CI/CD setup:** added a GitHub Actions workflow to run `forge fmt --check`, `forge build`, and
  `forge test` on every push, catching formatting and compile issues automatically.

## Status
✅ Compiles clean, CI passing
🚧 In progress — VRF mock deployment, Automation integration, and unit/fuzz tests still to come