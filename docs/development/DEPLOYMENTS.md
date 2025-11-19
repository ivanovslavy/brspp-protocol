# Deployment Guide

**Step-by-Step Deployment Procedures for Testnet and Mainnet**

[Documentation Index](../README.md) | [Development Timeline](./TIMELINE.md) | [Testing Strategy](./TESTING.md)

---

## Table of Contents

1. [Deployment Overview](#deployment-overview)
2. [Pre-Deployment Checklist](#pre-deployment-checklist)
3. [Testnet Deployment](#testnet-deployment)
4. [Mainnet Deployment](#mainnet-deployment)
5. [Contract Verification](#contract-verification)
6. [Post-Deployment Validation](#post-deployment-validation)
7. [Deployment Scripts](#deployment-scripts)
8. [Troubleshooting](#troubleshooting)

---

## Deployment Overview

### Deployment Strategy

BRSPP follows a phased deployment approach across multiple networks:

**Phase 1: Testnet Deployment (Month 10)**
- Sepolia (Ethereum testnet)
- BSC Testnet
- Polygon Amoy

**Phase 2: Mainnet Deployment (Month 15)**
- Ethereum Mainnet
- BSC Mainnet
- Polygon Mainnet

### Network Configuration

**Ethereum Mainnet:**
- Chain ID: 1
- RPC: https://mainnet.infura.io/v3/YOUR_KEY
- Explorer: https://etherscan.io
- Gas: Dynamic (EIP-1559)

**Sepolia Testnet:**
- Chain ID: 11155111
- RPC: https://sepolia.infura.io/v3/YOUR_KEY
- Explorer: https://sepolia.etherscan.io
- Gas: Dynamic (EIP-1559)
- Faucet: https://sepoliafaucet.com

**BSC Mainnet:**
- Chain ID: 56
- RPC: https://bsc-dataseed.binance.org
- Explorer: https://bscscan.com
- Gas: Standard (legacy)

**BSC Testnet:**
- Chain ID: 97
- RPC: https://data-seed-prebsc-1-s1.binance.org:8545
- Explorer: https://testnet.bscscan.com
- Faucet: https://testnet.binance.org/faucet-smart

**Polygon Mainnet:**
- Chain ID: 137
- RPC: https://polygon-rpc.com
- Explorer: https://polygonscan.com
- Gas: Standard (legacy)

**Polygon Amoy Testnet:**
- Chain ID: 80002
- RPC: https://rpc-amoy.polygon.technology
- Explorer: https://amoy.polygonscan.com
- Faucet: https://faucet.polygon.technology

---

## Pre-Deployment Checklist

### Code Preparation

**Smart Contracts:**
- [ ] All contracts compiled without errors
- [ ] Solidity version finalized (0.8.20)
- [ ] All tests passing (100+ tests)
- [ ] Code coverage >95%
- [ ] Gas optimization completed
- [ ] Security audit completed (mainnet only)
- [ ] Audit findings addressed (mainnet only)

**Configuration:**
- [ ] Contract constructor parameters documented
- [ ] Initial protocol fee set (100 = 1%)
- [ ] Payment limits configured
- [ ] Oracle addresses verified for target network
- [ ] FeeCollector owners and percentages determined

**Documentation:**
- [ ] Deployment script tested on local network
- [ ] Contract addresses spreadsheet prepared
- [ ] ABI export directory created
- [ ] Deployment runbook reviewed

### Environment Setup

**Development Environment:**
- [ ] Node.js v18+ installed
- [ ] Hardhat configured correctly
- [ ] .env file prepared with private keys
- [ ] RPC endpoints tested and responsive
- [ ] Sufficient native tokens for gas fees

**Gas Fee Budget:**

Testnet deployment (per network):
- Contract deployment: ~15M gas total
- Verification transactions: ~1M gas total
- Testing transactions: ~5M gas total
- Buffer: 5M gas
- Total: ~26M gas per network
- Cost: FREE (testnet tokens from faucet)

Mainnet deployment (per network):
- Contract deployment: ~15M gas total
- At 50 gwei: ~0.75 ETH (~$1,500 at $2,000/ETH)
- At 100 gwei: ~1.5 ETH (~$3,000)
- Budget per network: $2,000-3,000
- Total for 3 networks: $6,000-9,000

**Security Measures:**
- [ ] Deployment wallet is hardware wallet (Ledger/Trezor)
- [ ] Private keys never stored on internet-connected device
- [ ] Multi-signature wallet prepared for ownership transfer
- [ ] Backup of deployment keys in secure location

### External Dependencies

**Chainlink Oracle Addresses:**

Verify current oracle addresses before deployment:

Ethereum Mainnet:
- ETH/USD: 0x5f4eC3Df9cbd43714FE2740f5E3616155c5b8419
- USDC/USD: 0x8fFfFfd4AfB6115b954Bd326cbe7B4BA576818f6
- USDT/USD: 0x3E7d1eAB13ad0104d2750B8863b489D65364e32D
- DAI/USD: 0xAed0c38402a5d19df6E4c03F4E2DceD6e29c1ee9

BSC Mainnet:
- BNB/USD: 0x0567F2323251f0Aab15c8dFb1967E4e8A7D42aeE
- USDC/USD: 0x51597f405303C4377E36123cBc172b13269EA163
- USDT/USD: 0xB97Ad0E74fa7d920791E90258A6E2085088b4320
- DAI/USD: 0x132d3C0B1D2cEa0BC552588063bdBb210FDeecfA

Polygon Mainnet:
- MATIC/USD: 0xAB594600376Ec9fD91F8e885dADF0CE036862dE0
- USDC/USD: 0xfE4A8cc5b5B2366C1B58Bea3858e81843581b2F7
- USDT/USD: 0x0A6513e40db6EB1b165753AD52E80663aeA50545
- DAI/USD: 0x4746DeC9e833A82EC7C2C1356372CcF2cfcD2F3D

Testnet addresses may differ - verify at https://docs.chain.link

---

## Testnet Deployment

### Sepolia Deployment

**Step 1: Obtain Testnet ETH**
```bash
# Visit Sepolia faucet
# https://sepoliafaucet.com
# Enter deployment wallet address
# Wait for testnet ETH to arrive (usually <5 minutes)

# Verify balance
npx hardhat run scripts/checkBalance.js --network sepolia
```

**Step 2: Configure Environment**
```bash
# .env file
SEPOLIA_RPC_URL=https://sepolia.infura.io/v3/YOUR_INFURA_KEY
SEPOLIA_PRIVATE_KEY=your_private_key_here
ETHERSCAN_API_KEY=your_etherscan_api_key
```

**Step 3: Deploy Contracts**
```bash
# Deploy all contracts in correct order
npx hardhat run scripts/deploy.js --network sepolia

# Expected output:
# Deploying to Sepolia...
# Deploying ProtocolConfig...
# ProtocolConfig deployed to: 0x...
# Deploying MerchantRegistry...
# MerchantRegistry deployed to: 0x...
# [continues for all 8 contracts]
# Deployment complete!
# Total gas used: ~14,500,000
```

**Step 4: Verify Contracts**
```bash
# Verify all contracts on Etherscan
npx hardhat run scripts/verify.js --network sepolia

# Or verify individually
npx hardhat verify --network sepolia 0xProtocolConfigAddress
npx hardhat verify --network sepolia 0xMerchantRegistryAddress
# [repeat for all contracts]
```

**Step 5: Initialize Contracts**
```bash
# Run initialization script
npx hardhat run scripts/initialize.js --network sepolia

# This script:
# - Connects contracts to each other
# - Sets up initial configuration
# - Whitelists test merchants
# - Whitelists test tokens
```

**Step 6: Export ABIs and Addresses**
```bash
# Export contract ABIs
npx hardhat run scripts/exportABIs.js --network sepolia

# Output: abi/sepolia/
# - PaymentProcessor.json
# - TokenPaymentProcessor.json
# - [all contract ABIs]

# Export addresses
npx hardhat run scripts/exportAddresses.js --network sepolia

# Output: deployments/sepolia-addresses.json
```

**Step 7: Validation Testing**
```bash
# Run post-deployment tests
npx hardhat test test/integration/sepolia.test.js --network sepolia

# Test scenarios:
# - Whitelist test merchant
# - Process test payment
# - Verify payment completed
# - Check fee collection
# - Validate oracle integration
```

### BSC Testnet Deployment

Follow same steps as Sepolia with BSC-specific configuration:
```bash
# .env additions
BSC_TESTNET_RPC_URL=https://data-seed-prebsc-1-s1.binance.org:8545
BSC_TESTNET_PRIVATE_KEY=your_private_key_here
BSCSCAN_API_KEY=your_bscscan_api_key

# Deploy
npx hardhat run scripts/deploy.js --network bscTestnet

# Verify
npx hardhat run scripts/verify.js --network bscTestnet

# Initialize
npx hardhat run scripts/initialize.js --network bscTestnet

# Test
npx hardhat test test/integration/bsc.test.js --network bscTestnet
```

### Polygon Amoy Deployment

Follow same steps with Polygon Amoy configuration:
```bash
# .env additions
POLYGON_AMOY_RPC_URL=https://rpc-amoy.polygon.technology
POLYGON_AMOY_PRIVATE_KEY=your_private_key_here
POLYGONSCAN_API_KEY=your_polygonscan_api_key

# Deploy
npx hardhat run scripts/deploy.js --network polygonAmoy

# Verify
npx hardhat run scripts/verify.js --network polygonAmoy

# Initialize
npx hardhat run scripts/initialize.js --network polygonAmoy

# Test
npx hardhat test test/integration/polygon.test.js --network polygonAmoy
```

### Testnet Deployment Checklist

After deploying to all three testnets:

- [ ] All contracts deployed successfully on Sepolia
- [ ] All contracts deployed successfully on BSC Testnet
- [ ] All contracts deployed successfully on Polygon Amoy
- [ ] All contracts verified on block explorers
- [ ] Contract addresses documented in spreadsheet
- [ ] ABIs exported and stored
- [ ] Initialization completed on all networks
- [ ] Test payments successful on all networks
- [ ] Oracle price feeds working correctly
- [ ] Fee collection functional
- [ ] Backend event listener connected and working
- [ ] Frontend connected to testnet contracts
- [ ] Documentation updated with testnet addresses

---

## Mainnet Deployment

### Pre-Mainnet Security Review

**Critical Checkpoints:**

- [ ] External security audit completed
- [ ] All critical audit findings resolved
- [ ] All high severity findings resolved
- [ ] Medium severity findings evaluated and addressed or documented
- [ ] Testnet has been operational for minimum 1 month
- [ ] Minimum 100 test transactions processed on testnet
- [ ] No critical bugs discovered during testnet phase
- [ ] Bug bounty program prepared for post-launch
- [ ] Incident response plan documented
- [ ] Emergency pause procedures tested

### Mainnet Deployment Timeline

**Week 1: Final Preparation**

Day 1-2: Final code review
- Review all contract code one final time
- Verify no changes since audit
- Confirm all tests still passing
- Verify gas estimates

Day 3-4: Mainnet environment setup
- Setup mainnet RPC providers (Infura, Alchemy)
- Prepare deployment wallet with funds
- Configure hardware wallet for deployment
- Test connection to mainnet (read-only)

Day 5: Deployment rehearsal
- Run complete deployment on mainnet fork
- Verify all steps execute correctly
- Time each deployment step
- Document any issues

Day 6-7: Final preparations
- Transfer deployment funds to wallet
- Coordinate team availability
- Schedule deployment window
- Prepare communication materials

**Week 2: Mainnet Deployment**

Day 1: Ethereum Mainnet Deployment
Day 2: Ethereum validation and monitoring
Day 3: BSC Mainnet Deployment
Day 4: BSC validation and monitoring
Day 5: Polygon Mainnet Deployment
Day 6: Polygon validation and monitoring
Day 7: Cross-network testing and final validation

### Ethereum Mainnet Deployment

**Deployment Day Procedure:**

**T-60 minutes: Pre-deployment checks**
```bash
# Verify environment
node scripts/preDeploymentCheck.js --network ethereum

# Checklist output:
# ✓ RPC connection successful
# ✓ Deployment wallet balance sufficient (>2 ETH)
# ✓ Gas price acceptable (<100 gwei)
# ✓ Compiler version correct (0.8.20)
# ✓ All tests passing
# ✓ Code matches audited version
```

**T-30 minutes: Team coordination**

- Deployment lead confirms ready
- Monitoring team confirms systems ready
- Communication lead ready to announce
- Backup team member on standby

**T-0: Begin Deployment**
```bash
# Start deployment
npx hardhat run scripts/deploy.js --network ethereum

# Monitor deployment in real-time
# Estimated duration: 30-45 minutes for all 8 contracts
```

**Deployment Progress Log:**
```
[00:00] Starting deployment to Ethereum Mainnet
[00:02] Deploying ProtocolConfig...
[00:05] ProtocolConfig deployed: 0xABC...123
[00:06] Deploying MerchantRegistry...
[00:09] MerchantRegistry deployed: 0xDEF...456
[00:10] Deploying TokenWhitelist...
[00:13] TokenWhitelist deployed: 0xGHI...789
[00:14] Deploying FeeCollector...
[00:18] FeeCollector deployed: 0xJKL...012
[00:19] Deploying PriceOracle...
[00:22] PriceOracle deployed: 0xMNO...345
[00:23] Deploying FallbackPriceOracle...
[00:26] FallbackPriceOracle deployed: 0xPQR...678
[00:27] Deploying PaymentProcessor...
[00:32] PaymentProcessor deployed: 0xSTU...901
[00:33] Deploying TokenPaymentProcessor...
[00:38] TokenPaymentProcessor deployed: 0xVWX...234

[00:39] All contracts deployed successfully
[00:39] Total gas used: 14,523,456
[00:39] Total cost: 0.726 ETH ($1,452 at $2,000/ETH)

[00:40] Initializing contracts...
[00:42] Connecting contract references...
[00:44] Setting initial configuration...
[00:46] Initialization complete

[00:47] DEPLOYMENT SUCCESSFUL
```

**T+60 minutes: Verification**
```bash
# Verify all contracts on Etherscan
npx hardhat run scripts/verify.js --network ethereum

# Verify initialization
npx hardhat run scripts/validateDeployment.js --network ethereum

# Output:
# ✓ All contracts verified on Etherscan
# ✓ Contract references correct
# ✓ Initial configuration correct
# ✓ Oracle integration functional
# ✓ Fee collector setup correct
```

**T+120 minutes: Test Transaction**
```bash
# Execute controlled test payment
npx hardhat run scripts/testPayment.js --network ethereum

# Test with small amount (0.01 ETH)
# Verify:
# - Payment processes successfully
# - Events emitted correctly
# - Fee collection works
# - Oracle price accurate
```

### BSC Mainnet Deployment

Follow similar procedure as Ethereum with BSC-specific configuration.

**Key Differences:**
- Lower gas costs (typically <1 BNB for full deployment)
- Legacy transaction format (not EIP-1559)
- Different oracle addresses
- BSCScan verification instead of Etherscan
```bash
# Deploy to BSC Mainnet
npx hardhat run scripts/deploy.js --network bsc

# Verify on BSCScan
npx hardhat run scripts/verify.js --network bsc
```

### Polygon Mainnet Deployment

Follow similar procedure with Polygon-specific configuration.

**Key Differences:**
- Lowest gas costs (typically <50 MATIC for full deployment)
- Fastest block times (~2 seconds)
- Different oracle addresses
- PolygonScan verification
```bash
# Deploy to Polygon Mainnet
npx hardhat run scripts/deploy.js --network polygon

# Verify on PolygonScan
npx hardhat run scripts/verify.js --network polygon
```

### Post-Deployment Actions

**Immediate (within 1 hour):**

- [ ] Transfer contract ownership to multi-signature wallet
- [ ] Export and backup all contract addresses
- [ ] Export and backup all ABIs
- [ ] Update documentation with mainnet addresses
- [ ] Announce deployment to team
- [ ] Begin intensive monitoring (24/7 for first 48 hours)

**Within 24 hours:**

- [ ] Update frontend to use mainnet contracts
- [ ] Update backend to listen to mainnet events
- [ ] Test complete end-to-end flow on mainnet
- [ ] Update public documentation
- [ ] Announce mainnet launch publicly
- [ ] Activate bug bounty program

**Within 1 week:**

- [ ] Publish audit report
- [ ] Publish deployment transaction hashes
- [ ] Create deployment post-mortem document
- [ ] Review and optimize monitoring alerts
- [ ] Prepare merchant onboarding materials

---

## Contract Verification

### Etherscan Verification

**Automatic Verification:**
```javascript
// hardhat.config.js
module.exports = {
  etherscan: {
    apiKey: {
      mainnet: process.env.ETHERSCAN_API_KEY,
      sepolia: process.env.ETHERSCAN_API_KEY,
      bsc: process.env.BSCSCAN_API_KEY,
      bscTestnet: process.env.BSCSCAN_API_KEY,
      polygon: process.env.POLYGONSCAN_API_KEY,
      polygonAmoy: process.env.POLYGONSCAN_API_KEY
    }
  }
};
```
```bash
# Verify contract
npx hardhat verify --network ethereum 0xContractAddress "constructorArg1" "constructorArg2"

# Example: Verify PaymentProcessor
npx hardhat verify --network ethereum \
  0x742d35Cc6634C0532925a3b844Bc9e7595f0bEb \
  0xMerchantRegistryAddress \
  0xPriceOracleAddress \
  0xFallbackOracleAddress \
  0xFeeCollectorAddress \
  0xProtocolConfigAddress
```

**Manual Verification:**

If automatic verification fails:

1. Visit Etherscan contract page
2. Click "Contract" tab
3. Click "Verify and Publish"
4. Select "Solidity (Single file)"
5. Enter compiler version (0.8.20)
6. Enter optimization (Yes, 200 runs)
7. Paste flattened contract code
8. Enter constructor arguments (ABI-encoded)
9. Submit

**Flatten Contract for Manual Verification:**
```bash
# Flatten contract to single file
npx hardhat flatten contracts/PaymentProcessor.sol > PaymentProcessor_flat.sol

# Clean up duplicate SPDX license identifiers
# Remove duplicate pragma statements
# Remove duplicate imports
```

### Verification Checklist

After verification, confirm:

- [ ] Contract source code visible on block explorer
- [ ] Constructor arguments decoded correctly
- [ ] Read functions accessible
- [ ] Write functions accessible
- [ ] Events log properly
- [ ] Contract is marked as verified (green checkmark)

---

## Post-Deployment Validation

### Automated Validation Script
```javascript
// scripts/validateDeployment.js

async function validateDeployment(network) {
  console.log(`Validating deployment on ${network}...`);
  
  // Load deployed addresses
  const addresses = require(`../deployments/${network}-addresses.json`);
  
  // Test 1: Verify contract deployment
  console.log("Test 1: Contract deployment...");
  for (const [name, address] of Object.entries(addresses)) {
    const code = await ethers.provider.getCode(address);
    if (code === "0x") {
      throw new Error(`${name} not deployed at ${address}`);
    }
    console.log(`✓ ${name} deployed correctly`);
  }
  
  // Test 2: Verify contract connections
  console.log("Test 2: Contract connections...");
  const paymentProcessor = await ethers.getContractAt(
    "PaymentProcessor",
    addresses.PaymentProcessor
  );
  
  const merchantRegistry = await paymentProcessor.merchantRegistry();
  if (merchantRegistry !== addresses.MerchantRegistry) {
    throw new Error("MerchantRegistry not connected correctly");
  }
  console.log("✓ MerchantRegistry connected");
  
  const priceOracle = await paymentProcessor.priceOracle();
  if (priceOracle !== addresses.PriceOracle) {
    throw new Error("PriceOracle not connected correctly");
  }
  console.log("✓ PriceOracle connected");
  
  // [Continue for all connections...]
  
  // Test 3: Verify oracle functionality
  console.log("Test 3: Oracle functionality...");
  const oracle = await ethers.getContractAt("PriceOracle", addresses.PriceOracle);
  const price = await oracle.getLatestPrice();
  if (price.eq(0)) {
    throw new Error("Oracle returning zero price");
  }
  console.log(`✓ Oracle functional (price: ${ethers.formatUnits(price, 8)})`);
  
  // Test 4: Verify configuration
  console.log("Test 4: Protocol configuration...");
  const config = await ethers.getContractAt("ProtocolConfig", addresses.ProtocolConfig);
  const fee = await config.protocolFeePercentage();
  if (!fee.eq(100)) {
    throw new Error("Protocol fee not set correctly");
  }
  console.log("✓ Protocol fee correct (1%)");
  
  // Test 5: Verify ownership
  console.log("Test 5: Contract ownership...");
  const owner = await paymentProcessor.owner();
  console.log(`✓ Owner: ${owner}`);
  
  console.log("\n✅ All validation tests passed!");
}
```

### Manual Validation Checklist

**Contract Verification:**
- [ ] All 8 contracts deployed successfully
- [ ] All contracts verified on block explorer
- [ ] No deployment transaction reverted
- [ ] Total gas usage within expected range

**Configuration Verification:**
- [ ] Protocol fee set to 1% (100 basis points)
- [ ] Payment limits configured correctly
- [ ] Payment timeout set appropriately
- [ ] Payments enabled (not paused)

**Connection Verification:**
- [ ] PaymentProcessor references correct MerchantRegistry
- [ ] PaymentProcessor references correct PriceOracle
- [ ] PaymentProcessor references correct FallbackOracle
- [ ] PaymentProcessor references correct FeeCollector
- [ ] PaymentProcessor references correct ProtocolConfig
- [ ] TokenPaymentProcessor references all correct contracts
- [ ] Oracle references correct Chainlink aggregator

**Oracle Verification:**
- [ ] Primary oracle returns valid price
- [ ] Price is not stale (updated recently)
- [ ] Fallback oracle configured
- [ ] Oracle addresses match Chainlink documentation

**Access Control Verification:**
- [ ] Contract owner is correct address
- [ ] Multi-sig wallet configured (mainnet only)
- [ ] No unauthorized addresses have elevated permissions

**Initial State Verification:**
- [ ] No merchants whitelisted yet (or only test merchants)
- [ ] Correct tokens whitelisted (USDC, USDT, DAI)
- [ ] FeeCollector has correct owners and percentages
- [ ] No unexpected funds in contracts

---

## Deployment Scripts

### Main Deployment Script
```javascript
// scripts/deploy.js

const hre = require("hardhat");
const fs = require("fs");

async function main() {
  const network = hre.network.name;
  console.log(`Deploying to ${network}...`);
  
  // Get deployment configuration
  const config = require(`../config/${network}.json`);
  const [deployer] = await ethers.getSigners();
  
  console.log(`Deploying with account: ${deployer.address}`);
  console.log(`Account balance: ${ethers.formatEther(await ethers.provider.getBalance(deployer.address))} ETH`);
  
  // Deploy contracts in order
  
  // 1. ProtocolConfig
  console.log("\nDeploying ProtocolConfig...");
  const ProtocolConfig = await ethers.getContractFactory("ProtocolConfig");
  const protocolConfig = await ProtocolConfig.deploy();
  await protocolConfig.waitForDeployment();
  console.log(`ProtocolConfig deployed to: ${await protocolConfig.getAddress()}`);
  
  // 2. MerchantRegistry
  console.log("\nDeploying MerchantRegistry...");
  const MerchantRegistry = await ethers.getContractFactory("MerchantRegistry");
  const merchantRegistry = await MerchantRegistry.deploy();
  await merchantRegistry.waitForDeployment();
  console.log(`MerchantRegistry deployed to: ${await merchantRegistry.getAddress()}`);
  
  // 3. TokenWhitelist
  console.log("\nDeploying TokenWhitelist...");
  const TokenWhitelist = await ethers.getContractFactory("TokenWhitelist");
  const tokenWhitelist = await TokenWhitelist.deploy();
  await tokenWhitelist.waitForDeployment();
  console.log(`TokenWhitelist deployed to: ${await tokenWhitelist.getAddress()}`);
  
  // 4. FeeCollector
  console.log("\nDeploying FeeCollector...");
  const FeeCollector = await ethers.getContractFactory("FeeCollector");
  const feeCollector = await FeeCollector.deploy(
    config.feeCollectorOwners,
    config.feeCollectorPercentages
  );
  await feeCollector.waitForDeployment();
  console.log(`FeeCollector deployed to: ${await feeCollector.getAddress()}`);
  
  // 5. PriceOracle
  console.log("\nDeploying PriceOracle...");
  const PriceOracle = await ethers.getContractFactory("PriceOracle");
  const priceOracle = await PriceOracle.deploy(config.chainlinkOracleAddress);
  await priceOracle.waitForDeployment();
  console.log(`PriceOracle deployed to: ${await priceOracle.getAddress()}`);
  
  // 6. FallbackPriceOracle
  console.log("\nDeploying FallbackPriceOracle...");
  const FallbackPriceOracle = await ethers.getContractFactory("FallbackPriceOracle");
  const fallbackOracle = await FallbackPriceOracle.deploy();
  await fallbackOracle.waitForDeployment();
  console.log(`FallbackPriceOracle deployed to: ${await fallbackOracle.getAddress()}`);
  
  // 7. PaymentProcessor
  console.log("\nDeploying PaymentProcessor...");
  const PaymentProcessor = await ethers.getContractFactory("PaymentProcessor");
  const paymentProcessor = await PaymentProcessor.deploy(
    await merchantRegistry.getAddress(),
    await priceOracle.getAddress(),
    await fallbackOracle.getAddress(),
    await feeCollector.getAddress(),
    await protocolConfig.getAddress()
  );
  await paymentProcessor.waitForDeployment();
  console.log(`PaymentProcessor deployed to: ${await paymentProcessor.getAddress()}`);
  
  // 8. TokenPaymentProcessor
  console.log("\nDeploying TokenPaymentProcessor...");
  const TokenPaymentProcessor = await ethers.getContractFactory("TokenPaymentProcessor");
  const tokenPaymentProcessor = await TokenPaymentProcessor.deploy(
    await merchantRegistry.getAddress(),
    await tokenWhitelist.getAddress(),
    await priceOracle.getAddress(),
    await fallbackOracle.getAddress(),
    await feeCollector.getAddress(),
    await protocolConfig.getAddress()
  );
  await tokenPaymentProcessor.waitForDeployment();
  console.log(`TokenPaymentProcessor deployed to: ${await tokenPaymentProcessor.getAddress()}`);
  
  // Save addresses
  const addresses = {
    ProtocolConfig: await protocolConfig.getAddress(),
    MerchantRegistry: await merchantRegistry.getAddress(),
    TokenWhitelist: await tokenWhitelist.getAddress(),
    FeeCollector: await feeCollector.getAddress(),
    PriceOracle: await priceOracle.getAddress(),
    FallbackPriceOracle: await fallbackOracle.getAddress(),
    PaymentProcessor: await paymentProcessor.getAddress(),
    TokenPaymentProcessor: await tokenPaymentProcessor.getAddress()
  };
  
  const deploymentsDir = `./deployments`;
  if (!fs.existsSync(deploymentsDir)) {
    fs.mkdirSync(deploymentsDir);
  }
  
  fs.writeFileSync(
    `${deploymentsDir}/${network}-addresses.json`,
    JSON.stringify(addresses, null, 2)
  );
  
  console.log("\n✅ Deployment complete!");
  console.log(`Addresses saved to deployments/${network}-addresses.json`);
}

main()
  .then(() => process.exit(0))
  .catch((error) => {
    console.error(error);
    process.exit(1);
  });
```

### Initialization Script
```javascript
// scripts/initialize.js

async function main() {
  const network = hre.network.name;
  console.log(`Initializing contracts on ${network}...`);
  
  // Load addresses
  const addresses = require(`../deployments/${network}-addresses.json`);
  const config = require(`../config/${network}.json`);
  
  // Get contract instances
  const tokenWhitelist = await ethers.getContractAt("TokenWhitelist", addresses.TokenWhitelist);
  
  // Whitelist tokens
  console.log("\nWhitelisting tokens...");
  
  if (config.tokens.USDC) {
    const tx = await tokenWhitelist.addToken(
      config.tokens.USDC.address,
      "USDC",
      config.tokens.USDC.decimals
    );
    await tx.wait();
    console.log("✓ USDC whitelisted");
  }
  
  if (config.tokens.USDT) {
    const tx = await tokenWhitelist.addToken(
      config.tokens.USDT.address,
      "USDT",
      config.tokens.USDT.decimals
    );
    await tx.wait();
    console.log("✓ USDT whitelisted");
  }
  
  if (config.tokens.DAI) {
    const tx = await tokenWhitelist.addToken(
      config.tokens.DAI.address,
      "DAI",
      config.tokens.DAI.decimals
    );
    await tx.wait();
    console.log("✓ DAI whitelisted");
  }
  
  console.log("\n✅ Initialization complete!");
}
```

---

## Troubleshooting

### Common Deployment Issues

**Issue: Insufficient gas for deployment**

Symptoms:
- Transaction reverts with "out of gas" error
- Deployment stops mid-way

Solution:
```javascript
// Increase gas limit in hardhat.config.js
networks: {
  ethereum: {
    gas: 8000000,
    gasPrice: 50000000000 // 50 gwei
  }
}
```

**Issue: Nonce too low**

Symptoms:
- Transaction fails with "nonce too low" error

Solution:
```bash
# Reset nonce
npx hardhat run scripts/resetNonce.js --network ethereum

# Or manually specify nonce in deployment script
const tx = await contract.deploy({ nonce: correctNonce });
```

**Issue: Contract verification fails**

Symptoms:
- Etherscan shows "Contract source code not verified"

Solutions:
1. Check compiler version matches exactly (0.8.20)
2. Verify optimization settings (200 runs)
3. Ensure constructor arguments are correct
4. Try manual verification with flattened source
5. Wait a few minutes and retry (Etherscan may be slow)

**Issue: Oracle returns stale price**

Symptoms:
- Deployment succeeds but test payment fails
- Error: "Price data stale"

Solution:
```javascript
// Check oracle freshness threshold
const oracle = await ethers.getContractAt("PriceOracle", oracleAddress);
const threshold = await oracle.STALENESS_THRESHOLD();
console.log(`Staleness threshold: ${threshold} seconds`);

// Verify Chainlink oracle is updating
const chainlinkOracle = await ethers.getContractAt(
  "AggregatorV3Interface",
  chainlinkAddress
);
const roundData = await chainlinkOracle.latestRoundData();
console.log(`Last update: ${new Date(roundData.updatedAt * 1000)}`);
```

**Issue: Out of testnet tokens**

Symptoms:
- Cannot complete deployment on testnet
- Insufficient balance errors

Solution:
```bash
# Get more testnet tokens from faucets
# Sepolia: https://sepoliafaucet.com
# BSC Testnet: https://testnet.binance.org/faucet-smart
# Polygon Amoy: https://faucet.polygon.technology

# Or bridge from other testnets if available
```

### Emergency Procedures

**Emergency: Critical bug discovered post-deployment**

Immediate actions:
1. Pause all payment processors
2. Investigate issue severity
3. Prepare fix if possible
4. Communicate to team and users
5. Plan redeployment if necessary
```javascript
// Pause contracts
const paymentProcessor = await ethers.getContractAt("PaymentProcessor", address);
await paymentProcessor.pause();

const tokenProcessor = await ethers.getContractAt("TokenPaymentProcessor", address);
await tokenProcessor.pause();
```

**Emergency: Incorrect configuration**

If contracts deployed with wrong configuration:

Option A: Update via admin functions (if possible)
```javascript
const config = await ethers.getContractAt("ProtocolConfig", address);
await config.setProtocolFee(newFee);
```

Option B: Redeploy contracts (if configuration immutable)
- Pause old contracts
- Deploy new contracts
- Migrate data if necessary
- Update frontend/backend
- Communicate changes

---

## Related Documentation

**Architecture:**
- [System Overview](../architecture/OVERVIEW.md) - High-level architecture
- [Smart Contracts](../architecture/SMART-CONTRACTS.md) - Contract specifications

**Development:**
- [Development Timeline](./TIMELINE.md) - Project roadmap
- [Testing Strategy](./TESTING.md) - Test methodology

---

[Back to Documentation Index](../README.md) | [Main Repository](../../README.md)

---

**Last Updated:** November 2025  
**Version:** 1.0.0
