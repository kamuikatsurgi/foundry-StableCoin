# Foundry DeFi Stablecoin CodeHawks Audit Contest - Findings Report

# Table of contents

- ## [Contest Summary](#contest-summary)
- ## [Results Summary](#results-summary)
- ## High Risk Findings
  - ### [H-01. Tokens other than 18 decimals can not be used as collateral](#H-01)
- ## Medium Risk Findings
  - ### [M-01. depositCollateral does not work on non-standard compliant tokens like USDT](#M-01)

# <a id='contest-summary'></a>Contest Summary

### Sponsor: Cyfrin

### Dates: Jul 24th, 2023 - Aug 5th, 2023

[See more contest details here](https://www.codehawks.com/contests/cljx3b9390009liqwuedkn0m0)

# <a id='results-summary'></a>Results Summary

### Number of findings:

- High: 1
- Medium: 1
- Low: 0

# High Risk Findings

## <a id='H-01'></a>H-01. Tokens other than 18 decimals can not be used as collateral

### Relevant GitHub Links

https://github.com/Cyfrin/2023-07-foundry-defi-stablecoin/blob/d1c5501aa79320ca0aeaa73f47f0dbc88c7b77e2/src/DSCEngine.sol#L71

## Summary

User using tokens other than 18 decimals can have severe malfunctioning.

## Vulnerability Details

Considering that the smart contract can be used with any ERC20 token as collateral, it does not implement the same. The smart contract uses the PRECISION = 1e18 as a multiplier to calculate token amount and its usd amount. But if the smart contract is to work with all ERC20 tokens whose price feed is supported by Chainlink then it fails.

## Impact

If users attempt to use tokens that have decimals other than 18 as collateral, the smart contract will miscalculate the token's value and its equivalent in USD. This can lead to a number of problems:

1. Overvaluation or undervaluation of collateral
2. Market manipulation
3. Contract Failure

## Tools Used

1. VS Code
2. Manual Analysis
3. Hardhat

## Recommendations

# Dynamic Mechanism

Consider taking the decimals of each collateral token as an array in constructor and then multiply that particular precision value to calculate its amount and usd value.

# Medium Risk Findings

## <a id='M-01'></a>M-01. depositCollateral does not work on non-standard compliant tokens like USDT

### Relevant GitHub Links

https://github.com/Cyfrin/2023-07-foundry-defi-stablecoin/blob/d1c5501aa79320ca0aeaa73f47f0dbc88c7b77e2/src/DSCEngine.sol#L149-L161

https://github.com/Cyfrin/2023-07-foundry-defi-stablecoin/blob/d1c5501aa79320ca0aeaa73f47f0dbc88c7b77e2/src/DSCEngine.sol#L157

## Summary

The depositCollateral function in the smart contract fails with non-standard compliant tokens like USDT due to their void return type instead of a boolean.

## Vulnerability Details

The smart contract is designed to accept any asset which is well-recognized and has chainlink priceFeed as collateral for their stablecoin. But tokens like USDT does not follow standard EIP-20 format and does not return a boolean on trannferFrom function. Calling these functions with the correct EIP20 function signatures will always revert as it is done in the contract. Because of this, when you try to deposit USDT as collateral, the function reverts and users will not be able to deposit into the contract to mint stablecoin.

## Impact

The impact is that certain non standard tokens like USDT, will not be able to interact with the smart contract as expected.

## Tools Used

1. VS Code
2. Manual analysis

## Recommendations

1. There is no need to check for a boolean return value in this case as the function will automatically revert on failure, including in cases where the token is non-standard compliant.
2. Consider using OpenZeppelin’s SafeERC20 versions with the safeTransfer and safeTransferFrom functions that handle the return value check as well as non-standard-compliant tokens.
