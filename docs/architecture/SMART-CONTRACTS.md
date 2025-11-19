# Smart Contract Specifications

**Detailed Documentation for All Protocol Contracts**

[Documentation Index](../README.md) | [Architecture Overview](./OVERVIEW.md) | [Security](./SECURITY.md)

---

## Table of Contents

1. [Contract Overview](#contract-overview)
2. [PaymentProcessor](#paymentprocessor)
3. [TokenPaymentProcessor](#tokenpaymentprocessor)
4. [PriceOracle](#priceoracle)
5. [FallbackPriceOracle](#fallbackpriceoracle)
6. [MerchantRegistry](#merchantregistry)
7. [TokenWhitelist](#tokenwhitelist)
8. [FeeCollector](#feecollector)
9. [ProtocolConfig](#protocolconfig)

---

## Contract Overview

The BRSPP protocol consists of eight core smart contracts deployed identically across Ethereum, BSC, and Polygon networks.

### Contract Relationships
```
PaymentProcessor ────┐
                     ├──→ MerchantRegistry (whitelist check)
TokenPaymentProcessor┘   ├──→ PriceOracle (price feed)
                         ├──→ FallbackPriceOracle (backup price)
                         ├──→ FeeCollector (fee destination)
                         ├──→ ProtocolConfig (parameters)
                         └──→ TokenWhitelist (token validation)
```

### Deployment Order

Contracts must be deployed in the following sequence to satisfy constructor dependencies:

1. ProtocolConfig
2. MerchantRegistry
3. TokenWhitelist
4. FeeCollector
5. PriceOracle
6. FallbackPriceOracle
7. PaymentProcessor
8. TokenPaymentProcessor

---

## PaymentProcessor

Processes payments in native tokens (ETH, BNB, MATIC).

### Contract Information

**File:** `contracts/PaymentProcessor.sol`  
**Inherits:** `Ownable`, `ReentrancyGuard`, `Pausable`  
**License:** MIT

### State Variables
```solidity
MerchantRegistry public merchantRegistry;
PriceOracle public priceOracle;
FallbackPriceOracle public fallbackOracle;
FeeCollector public feeCollector;
ProtocolConfig public protocolConfig;

mapping(bytes32 => Payment) public payments;
mapping(address => uint256) public merchantTotalReceived;
mapping(address => uint256) public lastPaymentBlock;
```

### Data Structures
```solidity
struct Payment {
    bytes32 paymentId;
    address merchant;
    address customer;
    uint256 amountCrypto;
    uint256 amountFiat;
    uint256 feeAmount;
    uint256 merchantAmount;
    uint256 timestamp;
    PaymentStatus status;
}

enum PaymentStatus {
    Pending,
    Completed,
    Failed,
    Refunded
}
```

### Core Functions

#### processPayment

Processes a native token payment from customer to merchant.
```solidity
function processPayment(
    address merchant,
    uint256 amountFiat,
    string memory orderId
) external payable nonReentrant whenNotPaused returns (bytes32)
```

**Parameters:**
- `merchant` - Address of the merchant receiving payment
- `amountFiat` - Payment amount in fiat (EUR/USD) with 2 decimals (e.g., 5000 = 50.00 EUR)
- `orderId` - Merchant's order reference string

**Returns:**
- `bytes32` - Unique payment ID

**Requirements:**
- Merchant must be whitelisted in MerchantRegistry
- msg.value must be greater than zero
- Rate limiting: one payment per address per block
- Contract must not be paused

**Process:**
1. Validates merchant is whitelisted
2. Generates unique payment ID from merchant, customer, orderId, and timestamp
3. Gets crypto price from oracle (with fallback)
4. Calculates expected crypto amount based on fiat amount
5. Validates msg.value matches expected amount (with 2% tolerance)
6. Calculates protocol fee (1% of payment amount)
7. Transfers fee to FeeCollector
8. Transfers remaining amount to merchant
9. Records payment details in mapping
10. Emits PaymentCompleted event
11. Updates merchant statistics

**Events:**
```solidity
event PaymentCompleted(
    bytes32 indexed paymentId,
    address indexed merchant,
    address indexed customer,
    uint256 amountCrypto,
    uint256 amountFiat,
    uint256 feeAmount,
    uint256 merchantAmount,
    uint256 blockNumber,
    uint256 timestamp
);
```

**Gas Estimate:** ~150,000 gas

#### refundPayment

Allows merchant to refund a completed payment.
```solidity
function refundPayment(bytes32 paymentId) external nonReentrant
```

**Parameters:**
- `paymentId` - ID of payment to refund

**Requirements:**
- Caller must be the merchant who received the payment
- Payment must exist and be in Completed status
- Merchant must have sufficient balance

**Process:**
1. Validates caller is payment merchant
2. Validates payment status is Completed
3. Transfers original amount back to customer
4. Updates payment status to Refunded
5. Emits PaymentRefunded event

**Events:**
```solidity
event PaymentRefunded(
    bytes32 indexed paymentId,
    address indexed merchant,
    address indexed customer,
    uint256 amount,
    uint256 timestamp
);
```

**Gas Estimate:** ~80,000 gas

### Administrative Functions

#### updateMerchantRegistry

Updates the merchant registry contract address.
```solidity
function updateMerchantRegistry(address newRegistry) external onlyOwner
```

#### updatePriceOracle

Updates the primary price oracle contract address.
```solidity
function updatePriceOracle(address newOracle) external onlyOwner
```

#### updateFallbackOracle

Updates the fallback price oracle contract address.
```solidity
function updateFallbackOracle(address newOracle) external onlyOwner
```

#### updateFeeCollector

Updates the fee collector contract address.
```solidity
function updateFeeCollector(address newCollector) external onlyOwner
```

#### pause / unpause

Emergency functions to halt or resume payment processing.
```solidity
function pause() external onlyOwner
function unpause() external onlyOwner
```

### View Functions

#### getPayment

Retrieves complete payment details.
```solidity
function getPayment(bytes32 paymentId) external view returns (Payment memory)
```

#### getMerchantStats

Returns statistics for a specific merchant.
```solidity
function getMerchantStats(address merchant) external view returns (
    uint256 totalReceived,
    uint256 lastPaymentBlock
)
```

### Security Features

- Reentrancy protection on all state-changing functions
- Rate limiting (one payment per address per block)
- Pausable for emergency situations
- Access control on administrative functions
- Input validation on all parameters
- Overflow protection (Solidity 0.8+)

---

## TokenPaymentProcessor

Processes payments in ERC20 tokens (USDC, USDT, DAI).

### Contract Information

**File:** `contracts/TokenPaymentProcessor.sol`  
**Inherits:** `Ownable`, `ReentrancyGuard`, `Pausable`  
**License:** MIT

### State Variables
```solidity
MerchantRegistry public merchantRegistry;
TokenWhitelist public tokenWhitelist;
PriceOracle public priceOracle;
FallbackPriceOracle public fallbackOracle;
FeeCollector public feeCollector;
ProtocolConfig public protocolConfig;

mapping(bytes32 => TokenPayment) public payments;
mapping(address => mapping(address => uint256)) public merchantTokenReceived;
```

### Data Structures
```solidity
struct TokenPayment {
    bytes32 paymentId;
    address merchant;
    address customer;
    address token;
    uint256 amountTokens;
    uint256 amountFiat;
    uint256 feeAmount;
    uint256 merchantAmount;
    uint256 timestamp;
    PaymentStatus status;
}
```

### Core Functions

#### processTokenPayment

Processes an ERC20 token payment from customer to merchant.
```solidity
function processTokenPayment(
    address merchant,
    address token,
    uint256 amountTokens,
    uint256 amountFiat,
    string memory orderId
) external nonReentrant whenNotPaused returns (bytes32)
```

**Parameters:**
- `merchant` - Address of merchant receiving payment
- `token` - ERC20 token contract address
- `amountTokens` - Amount of tokens to transfer (with token decimals)
- `amountFiat` - Payment amount in fiat (EUR/USD) with 2 decimals
- `orderId` - Merchant's order reference

**Returns:**
- `bytes32` - Unique payment ID

**Requirements:**
- Merchant must be whitelisted
- Token must be whitelisted
- Customer must have approved TokenPaymentProcessor to spend tokens
- Customer must have sufficient token balance
- Contract must not be paused

**Process:**
1. Validates merchant is whitelisted
2. Validates token is whitelisted
3. Generates unique payment ID
4. Calculates protocol fee (1% of token amount)
5. Transfers fee tokens to FeeCollector
6. Transfers remaining tokens to merchant
7. Records payment details
8. Emits TokenPaymentCompleted event
9. Updates merchant statistics

**Events:**
```solidity
event TokenPaymentCompleted(
    bytes32 indexed paymentId,
    address indexed merchant,
    address indexed customer,
    address token,
    uint256 amountTokens,
    uint256 amountFiat,
    uint256 feeAmount,
    uint256 merchantAmount,
    uint256 timestamp
);
```

**Gas Estimate:** ~180,000 gas (includes two token transfers)

#### refundTokenPayment

Allows merchant to refund a token payment.
```solidity
function refundTokenPayment(bytes32 paymentId) external nonReentrant
```

**Parameters:**
- `paymentId` - ID of payment to refund

**Requirements:**
- Caller must be the merchant
- Payment must be in Completed status
- Merchant must have approved contract to spend refund amount
- Merchant must have sufficient token balance

**Process:**
1. Validates caller is merchant
2. Validates payment status
3. Transfers original token amount back to customer
4. Updates payment status to Refunded
5. Emits TokenPaymentRefunded event

**Events:**
```solidity
event TokenPaymentRefunded(
    bytes32 indexed paymentId,
    address indexed merchant,
    address indexed customer,
    address token,
    uint256 amount,
    uint256 timestamp
);
```

**Gas Estimate:** ~100,000 gas

### Token Approval Requirements

Customers must approve TokenPaymentProcessor before making payments:
```solidity
// Customer approves USDC spending
IERC20(usdcAddress).approve(tokenPaymentProcessorAddress, amountTokens);

// Then process payment
tokenPaymentProcessor.processTokenPayment(merchant, usdcAddress, amountTokens, amountFiat, orderId);
```

### Administrative Functions

Same pattern as PaymentProcessor:
- `updateMerchantRegistry(address)`
- `updateTokenWhitelist(address)`
- `updatePriceOracle(address)`
- `updateFallbackOracle(address)`
- `updateFeeCollector(address)`
- `pause()` / `unpause()`

### View Functions

#### getTokenPayment

Retrieves complete token payment details.
```solidity
function getTokenPayment(bytes32 paymentId) external view returns (TokenPayment memory)
```

#### getMerchantTokenStats

Returns statistics for a merchant's specific token.
```solidity
function getMerchantTokenStats(address merchant, address token) external view returns (uint256)
```

---

## PriceOracle

Integrates Chainlink price feeds for EUR/USD to cryptocurrency conversions.

### Contract Information

**File:** `contracts/PriceOracle.sol`  
**Inherits:** `Ownable`  
**License:** MIT

### State Variables
```solidity
AggregatorV3Interface public priceFeed;
uint256 public constant PRICE_DECIMALS = 8;
uint256 public constant STALENESS_THRESHOLD = 3600; // 1 hour
```

### Constructor
```solidity
constructor(address chainlinkAggregator)
```

**Parameters:**
- `chainlinkAggregator` - Address of Chainlink price feed aggregator

**Network-Specific Aggregators:**

**Ethereum Mainnet:**
- ETH/USD: `0x5f4eC3Df9cbd43714FE2740f5E3616155c5b8419`

**BSC Mainnet:**
- BNB/USD: `0x0567F2323251f0Aab15c8dFb1967E4e8A7D42aeE`

**Polygon Mainnet:**
- MATIC/USD: `0xAB594600376Ec9fD91F8e885dADF0CE036862dE0`

### Core Functions

#### getLatestPrice

Retrieves the latest price from Chainlink oracle.
```solidity
function getLatestPrice() external view returns (uint256)
```

**Returns:**
- `uint256` - Latest price with 8 decimals

**Process:**
1. Calls Chainlink aggregator's `latestRoundData()`
2. Validates price is not zero
3. Validates price is not stale (updated within last hour)
4. Returns price

**Reverts:**
- If price is zero
- If price data is stale
- If oracle call fails

**Example:**
```solidity
uint256 ethPrice = priceOracle.getLatestPrice();
// ethPrice = 250000000000 (represents $2,500.00 with 8 decimals)
```

#### getPriceWithTimestamp

Returns price along with update timestamp.
```solidity
function getPriceWithTimestamp() external view returns (uint256 price, uint256 timestamp)
```

**Returns:**
- `price` - Latest price with 8 decimals
- `timestamp` - Unix timestamp of price update

### Administrative Functions

#### updatePriceFeed

Updates the Chainlink aggregator address.
```solidity
function updatePriceFeed(address newPriceFeed) external onlyOwner
```

#### updateStalenessThreshold

Updates the staleness check threshold.
```solidity
function updateStalenessThreshold(uint256 newThreshold) external onlyOwner
```

---

## FallbackPriceOracle

Backup oracle for high availability when primary oracle fails.

### Contract Information

**File:** `contracts/FallbackPriceOracle.sol`  
**Inherits:** `Ownable`  
**License:** MIT

### State Variables
```solidity
uint256 public manualPrice;
uint256 public lastUpdateTimestamp;
uint256 public constant PRICE_DECIMALS = 8;
```

### Core Functions

#### getLatestPrice

Returns the manually set fallback price.
```solidity
function getLatestPrice() external view returns (uint256)
```

**Returns:**
- `uint256` - Fallback price with 8 decimals

**Note:** This oracle requires manual price updates by contract owner.

#### setPrice

Owner sets the fallback price manually.
```solidity
function setPrice(uint256 newPrice) external onlyOwner
```

**Parameters:**
- `newPrice` - New price with 8 decimals

**Events:**
```solidity
event PriceUpdated(uint256 newPrice, uint256 timestamp);
```

**Usage:**
Fallback oracle is used when primary Chainlink oracle fails. Owner must monitor primary oracle health and update fallback price regularly during outages.

---

## MerchantRegistry

Manages whitelist of authorized merchants.

### Contract Information

**File:** `contracts/MerchantRegistry.sol`  
**Inherits:** `Ownable`  
**License:** MIT

### State Variables
```solidity
mapping(address => bool) public whitelistedMerchants;
mapping(address => MerchantInfo) public merchantInfo;
address[] public allMerchants;
```

### Data Structures
```solidity
struct MerchantInfo {
    address merchantAddress;
    string businessName;
    uint256 whitelistedAt;
    bool isActive;
}
```

### Core Functions

#### whitelistMerchant

Adds a merchant to the whitelist.
```solidity
function whitelistMerchant(address merchant, string memory businessName) external onlyOwner
```

**Parameters:**
- `merchant` - Ethereum address of merchant
- `businessName` - Business name for reference

**Process:**
1. Validates merchant address is not zero
2. Validates merchant is not already whitelisted
3. Adds merchant to whitelist
4. Records merchant info
5. Adds to allMerchants array
6. Emits MerchantWhitelisted event

**Events:**
```solidity
event MerchantWhitelisted(address indexed merchant, string businessName, uint256 timestamp);
```

#### removeMerchant

Removes a merchant from the whitelist.
```solidity
function removeMerchant(address merchant) external onlyOwner
```

**Parameters:**
- `merchant` - Address to remove

**Process:**
1. Validates merchant is currently whitelisted
2. Sets whitelistedMerchants[merchant] to false
3. Sets merchantInfo[merchant].isActive to false
4. Emits MerchantRemoved event

**Events:**
```solidity
event MerchantRemoved(address indexed merchant, uint256 timestamp);
```

**Note:** Removal does not delete historical data, only deactivates merchant.

### View Functions

#### isWhitelisted

Checks if an address is a whitelisted merchant.
```solidity
function isWhitelisted(address merchant) external view returns (bool)
```

#### getMerchantInfo

Returns complete merchant information.
```solidity
function getMerchantInfo(address merchant) external view returns (MerchantInfo memory)
```

#### getAllMerchants

Returns array of all merchant addresses (active and inactive).
```solidity
function getAllMerchants() external view returns (address[] memory)
```

#### getActiveMerchantCount

Returns count of currently active merchants.
```solidity
function getActiveMerchantCount() external view returns (uint256)
```

---

## TokenWhitelist

Manages list of approved ERC20 payment tokens.

### Contract Information

**File:** `contracts/TokenWhitelist.sol`  
**Inherits:** `Ownable`  
**License:** MIT

### State Variables
```solidity
mapping(address => bool) public whitelistedTokens;
mapping(address => TokenInfo) public tokenInfo;
address[] public allTokens;
```

### Data Structures
```solidity
struct TokenInfo {
    address tokenAddress;
    string symbol;
    uint8 decimals;
    uint256 whitelistedAt;
    bool isActive;
}
```

### Core Functions

#### addToken

Adds an ERC20 token to the whitelist.
```solidity
function addToken(address token, string memory symbol, uint8 decimals) external onlyOwner
```

**Parameters:**
- `token` - ERC20 contract address
- `symbol` - Token symbol (e.g., "USDC")
- `decimals` - Token decimals (e.g., 6 for USDC)

**Process:**
1. Validates token address is not zero
2. Validates token is not already whitelisted
3. Adds token to whitelist
4. Records token info
5. Adds to allTokens array
6. Emits TokenWhitelisted event

**Events:**
```solidity
event TokenWhitelisted(address indexed token, string symbol, uint8 decimals, uint256 timestamp);
```

#### removeToken

Removes a token from the whitelist.
```solidity
function removeToken(address token) external onlyOwner
```

**Parameters:**
- `token` - Token address to remove

**Process:**
1. Validates token is currently whitelisted
2. Sets whitelistedTokens[token] to false
3. Sets tokenInfo[token].isActive to false
4. Emits TokenRemoved event

**Events:**
```solidity
event TokenRemoved(address indexed token, uint256 timestamp);
```

### View Functions

#### isWhitelisted

Checks if a token is whitelisted.
```solidity
function isWhitelisted(address token) external view returns (bool)
```

#### getTokenInfo

Returns complete token information.
```solidity
function getTokenInfo(address token) external view returns (TokenInfo memory)
```

#### getAllTokens

Returns array of all token addresses.
```solidity
function getAllTokens() external view returns (address[] memory)
```

#### getActiveTokenCount

Returns count of currently active tokens.
```solidity
function getActiveTokenCount() external view returns (uint256)
```

---

## FeeCollector

Multi-owner fee collection and distribution contract.

### Contract Information

**File:** `contracts/FeeCollector.sol`  
**Inherits:** `Ownable`, `ReentrancyGuard`  
**License:** MIT

### State Variables
```solidity
address[] public owners;
uint256[] public percentages;
uint256 public constant PERCENTAGE_BASE = 10000; // 100.00%
mapping(address => uint256) public ownerIndex;
```

### Constructor
```solidity
constructor(address[] memory _owners, uint256[] memory _percentages)
```

**Parameters:**
- `_owners` - Array of owner addresses
- `_percentages` - Array of percentage allocations (basis points)

**Requirements:**
- Arrays must have equal length
- Percentages must sum to 10000 (100%)
- All addresses must be non-zero
- No duplicate addresses

**Example:**
```solidity
address[] memory owners = [0xOwner1, 0xOwner2];
uint256[] memory percentages = [7000, 3000]; // 70% and 30%
FeeCollector feeCollector = new FeeCollector(owners, percentages);
```

### Core Functions

#### withdrawAll

Distributes all accumulated native tokens to owners based on percentages.
```solidity
function withdrawAll() external nonReentrant
```

**Requirements:**
- Contract must have non-zero balance

**Process:**
1. Gets current contract balance
2. For each owner:
   - Calculates their share: balance × percentage / 10000
   - Transfers their share
3. Emits FeesDistributed event

**Events:**
```solidity
event FeesDistributed(uint256 totalAmount, uint256 timestamp);
event OwnerWithdrawal(address indexed owner, uint256 amount, uint256 timestamp);
```

**Gas Estimate:** ~50,000 gas per owner

#### withdrawToken

Distributes accumulated ERC20 tokens to owners.
```solidity
function withdrawToken(address token) external nonReentrant
```

**Parameters:**
- `token` - ERC20 token address

**Process:**
1. Gets token balance of contract
2. Distributes to owners based on percentages
3. Emits TokenFeesDistributed event

**Events:**
```solidity
event TokenFeesDistributed(address indexed token, uint256 totalAmount, uint256 timestamp);
```

### Governance Functions

#### proposePercentageChange

Proposes new percentage allocation.
```solidity
function proposePercentageChange(
    address[] memory newOwners,
    uint256[] memory newPercentages
) external returns (uint256 proposalId)
```

**Parameters:**
- `newOwners` - New array of owner addresses
- `newPercentages` - New percentage allocations

**Requirements:**
- Caller must be current owner
- Arrays must be valid (same length, sum to 10000)

**Returns:**
- `uint256` - Proposal ID for voting

#### approveProposal

Current owner approves a percentage change proposal.
```solidity
function approveProposal(uint256 proposalId) external
```

**Requirements:**
- Caller must be current owner
- Proposal must exist and be pending
- Caller has not already approved

**Process:**
1. Records approval
2. If all owners approved:
   - Executes percentage change
   - Updates owners and percentages
   - Emits PercentagesUpdated event

**Events:**
```solidity
event ProposalCreated(uint256 indexed proposalId, address proposer, uint256 timestamp);
event ProposalApproved(uint256 indexed proposalId, address approver, uint256 timestamp);
event PercentagesUpdated(address[] newOwners, uint256[] newPercentages, uint256 timestamp);
```

### View Functions

#### getOwners

Returns all owner addresses.
```solidity
function getOwners() external view returns (address[] memory)
```

#### getPercentages

Returns all percentage allocations.
```solidity
function getPercentages() external view returns (uint256[] memory)
```

#### getOwnerPercentage

Returns percentage for specific owner.
```solidity
function getOwnerPercentage(address owner) external view returns (uint256)
```

#### getContractBalance

Returns current native token balance.
```solidity
function getContractBalance() external view returns (uint256)
```

#### getTokenBalance

Returns balance for specific ERC20 token.
```solidity
function getTokenBalance(address token) external view returns (uint256)
```

---

## ProtocolConfig

Stores upgradeable protocol parameters.

### Contract Information

**File:** `contracts/ProtocolConfig.sol`  
**Inherits:** `Ownable`  
**License:** MIT

### State Variables
```solidity
uint256 public protocolFeePercentage; // Basis points (100 = 1%)
uint256 public maxPaymentAmount;
uint256 public minPaymentAmount;
uint256 public paymentTimeout;
bool public paymentsEnabled;
```

### Constructor
```solidity
constructor()
```

**Initial Values:**
- `protocolFeePercentage`: 100 (1%)
- `maxPaymentAmount`: 1,000,000 EUR (with 2 decimals = 100000000)
- `minPaymentAmount`: 1 EUR (with 2 decimals = 100)
- `paymentTimeout`: 3600 seconds (1 hour)
- `paymentsEnabled`: true

### Core Functions

#### setProtocolFee

Updates the protocol fee percentage.
```solidity
function setProtocolFee(uint256 newFeePercentage) external onlyOwner
```

**Parameters:**
- `newFeePercentage` - New fee in basis points (100 = 1%)

**Requirements:**
- Must be between 0 and 1000 (0% to 10%)

**Events:**
```solidity
event ProtocolFeeUpdated(uint256 oldFee, uint256 newFee, uint256 timestamp);
```

#### setPaymentLimits

Updates min and max payment amounts.
```solidity
function setPaymentLimits(uint256 min, uint256 max) external onlyOwner
```

**Parameters:**
- `min` - Minimum payment amount (fiat with 2 decimals)
- `max` - Maximum payment amount (fiat with 2 decimals)

**Requirements:**
- min must be less than max
- Both must be greater than zero

**Events:**
```solidity
event PaymentLimitsUpdated(uint256 minAmount, uint256 maxAmount, uint256 timestamp);
```

#### setPaymentTimeout

Updates payment timeout duration.
```solidity
function setPaymentTimeout(uint256 newTimeout) external onlyOwner
```

**Parameters:**
- `newTimeout` - Timeout in seconds

**Requirements:**
- Must be between 300 (5 minutes) and 7200 (2 hours)

**Events:**
```solidity
event PaymentTimeoutUpdated(uint256 oldTimeout, uint256 newTimeout, uint256 timestamp);
```

#### togglePayments

Enables or disables all payments globally.
```solidity
function togglePayments(bool enabled) external onlyOwner
```

**Parameters:**
- `enabled` - True to enable, false to disable

**Events:**
```solidity
event PaymentsToggled(bool enabled, uint256 timestamp);
```

### View Functions

#### getProtocolFee

Returns current protocol fee percentage.
```solidity
function getProtocolFee() external view returns (uint256)
```

#### getPaymentLimits

Returns min and max payment amounts.
```solidity
function getPaymentLimits() external view returns (uint256 min, uint256 max)
```

#### isPaymentValid

Validates if a payment amount is within limits.
```solidity
function isPaymentValid(uint256 amount) external view returns (bool)
```

---

## Related Documentation

**Architecture:**
- [System Overview](./OVERVIEW.md) - High-level architecture and design
- [Security Mechanisms](./SECURITY.md) - Security features and threat models

**Development:**
- [Testing Strategy](../development/TESTING.md) - Test coverage and methodology
- [Deployment Guide](../development/DEPLOYMENTS.md) - Deployment procedures

---

[Back to Documentation Index](../README.md) | [Main Repository](../../README.md)

---

**Last Updated:** November 2025  
**Version:** 1.0.0
