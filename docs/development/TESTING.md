# Testing Strategy

**Test Coverage and Methodology**

[Documentation Index](../README.md) | [Development Timeline](./TIMELINE.md) | [Deployment Guide](./DEPLOYMENTS.md)

---

## Table of Contents

1. [Testing Philosophy](#testing-philosophy)
2. [Test Categories](#test-categories)
3. [Unit Testing](#unit-testing)
4. [Integration Testing](#integration-testing)
5. [Security Testing](#security-testing)
6. [Performance Testing](#performance-testing)
7. [Test Coverage Requirements](#test-coverage-requirements)
8. [Testing Tools](#testing-tools)

---

## Testing Philosophy

### Test-Driven Development

Tests are written alongside or before implementation code to ensure functionality meets requirements and to catch regressions early.

**Principles:**
- Write tests first when possible
- Each function has corresponding tests
- Tests document expected behavior
- Tests catch edge cases and boundary conditions

### Comprehensive Coverage

Target 95%+ code coverage across all smart contracts with emphasis on critical paths and security-sensitive operations.

**Coverage Goals:**
- Branch coverage: 95%+
- Statement coverage: 95%+
- Function coverage: 100%
- Line coverage: 95%+

### Automated Testing

All tests run automatically on every commit through continuous integration pipeline.

**Automation:**
- Pre-commit hooks run linter and quick tests
- Push triggers full test suite
- Pull requests require all tests passing
- Daily runs include extended test suites

---

## Test Categories

### Category Overview
```
Unit Tests (60% of total tests)
├─ Individual function testing
├─ Input validation
├─ State changes
└─ Event emissions

Integration Tests (25% of total tests)
├─ Multi-contract interactions
├─ End-to-end payment flows
├─ Cross-chain scenarios
└─ External dependencies

Security Tests (10% of total tests)
├─ Attack simulations
├─ Access control verification
├─ Reentrancy testing
└─ Edge case exploitation

Performance Tests (5% of total tests)
├─ Gas optimization verification
├─ Load testing
├─ Scalability validation
└─ Benchmarking
```

### Test Distribution by Contract

**PaymentProcessor:**
- Unit tests: 25
- Integration tests: 10
- Security tests: 8
- Total: 43 tests

**TokenPaymentProcessor:**
- Unit tests: 22
- Integration tests: 8
- Security tests: 6
- Total: 36 tests

**PriceOracle:**
- Unit tests: 10
- Integration tests: 5
- Security tests: 3
- Total: 18 tests

**FallbackPriceOracle:**
- Unit tests: 6
- Integration tests: 2
- Security tests: 2
- Total: 10 tests

**MerchantRegistry:**
- Unit tests: 8
- Integration tests: 3
- Security tests: 4
- Total: 15 tests

**TokenWhitelist:**
- Unit tests: 8
- Integration tests: 3
- Security tests: 3
- Total: 14 tests

**FeeCollector:**
- Unit tests: 12
- Integration tests: 6
- Security tests: 4
- Total: 22 tests

**ProtocolConfig:**
- Unit tests: 8
- Integration tests: 2
- Security tests: 2
- Total: 12 tests

**Grand Total: 170+ tests**

---

## Unit Testing

### Test Structure

Standard test structure using Mocha and Chai:
```javascript
describe("PaymentProcessor", function() {
  let paymentProcessor;
  let merchantRegistry;
  let priceOracle;
  let owner;
  let merchant;
  let customer;

  beforeEach(async function() {
    // Deploy contracts
    // Setup accounts
    // Initialize state
  });

  describe("processPayment", function() {
    it("Should process payment successfully", async function() {
      // Test implementation
    });

    it("Should revert if merchant not whitelisted", async function() {
      // Test implementation
    });

    it("Should emit PaymentCompleted event", async function() {
      // Test implementation
    });
  });
});
```

### PaymentProcessor Unit Tests

**Function: processPayment**

Test cases:
1. Should process valid payment successfully
2. Should revert if merchant not whitelisted
3. Should revert if payment amount is zero
4. Should revert with invalid merchant address
5. Should revert if msg.value is zero
6. Should calculate correct fee amount (1%)
7. Should transfer correct amount to merchant
8. Should transfer fee to FeeCollector
9. Should emit PaymentCompleted event with correct parameters
10. Should update merchant total received
11. Should enforce rate limiting (one per block)
12. Should revert when paused
13. Should handle price tolerance correctly (±2%)
14. Should revert with stale oracle price
15. Should use fallback oracle when primary fails
16. Should generate unique payment IDs
17. Should store payment details correctly
18. Should handle large payment amounts
19. Should handle minimum payment amounts
20. Should prevent payment to zero address
21. Should revert with invalid order ID
22. Should record correct timestamp
23. Should update last payment block
24. Should maintain payment status
25. Should handle concurrent payments from different addresses

**Function: refundPayment**

Test cases:
1. Should allow merchant to refund payment
2. Should revert if caller is not merchant
3. Should revert if payment does not exist
4. Should revert if payment already refunded
5. Should revert if payment not completed
6. Should transfer refund to customer
7. Should emit PaymentRefunded event
8. Should update payment status to Refunded
9. Should handle partial refunds if implemented
10. Should prevent double refunds

**Administrative Functions**

Test cases:
1. Should allow owner to update merchant registry
2. Should allow owner to update price oracle
3. Should allow owner to update fee collector
4. Should allow owner to pause contract
5. Should allow owner to unpause contract
6. Should revert non-owner administrative calls
7. Should emit appropriate events for updates

### TokenPaymentProcessor Unit Tests

**Function: processTokenPayment**

Test cases:
1. Should process token payment successfully
2. Should revert if merchant not whitelisted
3. Should revert if token not whitelisted
4. Should revert with insufficient token approval
5. Should revert with insufficient token balance
6. Should calculate correct fee in tokens
7. Should transfer correct token amount to merchant
8. Should transfer token fee to FeeCollector
9. Should emit TokenPaymentCompleted event
10. Should update merchant token received statistics
11. Should handle different token decimals (6, 8, 18)
12. Should enforce rate limiting
13. Should revert when paused
14. Should generate unique payment IDs
15. Should store payment details correctly
16. Should handle USDC payments
17. Should handle USDT payments
18. Should handle DAI payments
19. Should prevent payment with zero token address
20. Should validate token amounts
21. Should handle token transfer failures gracefully
22. Should record correct timestamp

**Function: refundTokenPayment**

Test cases:
1. Should allow merchant to refund token payment
2. Should revert if caller is not merchant
3. Should require merchant token approval for refund
4. Should transfer tokens back to customer
5. Should emit TokenPaymentRefunded event
6. Should update payment status
7. Should prevent double refunds

### Oracle Unit Tests

**PriceOracle Tests:**

1. Should return valid price from Chainlink
2. Should revert with zero price
3. Should revert with stale price (>1 hour)
4. Should return price with 8 decimals
5. Should validate price timestamp
6. Should handle Chainlink oracle failure
7. Should allow owner to update price feed
8. Should allow owner to update staleness threshold
9. Should emit events on updates
10. Should revert non-owner updates

**FallbackPriceOracle Tests:**

1. Should allow owner to set manual price
2. Should return manually set price
3. Should emit PriceUpdated event
4. Should revert non-owner price updates
5. Should track last update timestamp
6. Should validate price is not zero

### Registry Unit Tests

**MerchantRegistry Tests:**

1. Should whitelist merchant successfully
2. Should store merchant info correctly
3. Should emit MerchantWhitelisted event
4. Should allow owner to remove merchant
5. Should emit MerchantRemoved event
6. Should prevent duplicate whitelisting
7. Should return correct whitelist status
8. Should return all merchants array
9. Should count active merchants correctly
10. Should prevent zero address whitelisting
11. Should validate business name
12. Should record whitelisting timestamp
13. Should maintain merchant info after removal
14. Should revert non-owner whitelist calls
15. Should handle multiple merchants correctly

**TokenWhitelist Tests:**

1. Should add token successfully
2. Should store token info (symbol, decimals)
3. Should emit TokenWhitelisted event
4. Should allow owner to remove token
5. Should emit TokenRemoved event
6. Should prevent duplicate token addition
7. Should return correct token whitelist status
8. Should return all tokens array
9. Should count active tokens correctly
10. Should prevent zero address tokens
11. Should validate token symbol and decimals
12. Should record whitelisting timestamp
13. Should maintain token info after removal
14. Should revert non-owner calls

### FeeCollector Unit Tests

1. Should deploy with correct owners and percentages
2. Should validate percentages sum to 10000
3. Should revert with mismatched array lengths
4. Should revert with zero addresses
5. Should revert with duplicate owners
6. Should receive native token payments
7. Should receive ERC20 token payments
8. Should distribute native tokens correctly
9. Should distribute ERC20 tokens correctly
10. Should calculate percentage splits accurately
11. Should emit FeesDistributed event
12. Should emit OwnerWithdrawal events
13. Should allow percentage change proposals
14. Should require all owners to approve changes
15. Should execute approved percentage changes
16. Should prevent unauthorized percentage changes
17. Should handle 2-owner split correctly
18. Should handle 3-owner split correctly
19. Should handle rounding in distributions
20. Should revert withdrawal with zero balance
21. Should track proposal approvals
22. Should prevent double approval

### ProtocolConfig Unit Tests

1. Should initialize with default values
2. Should allow owner to update protocol fee
3. Should validate fee percentage bounds (0-10%)
4. Should emit ProtocolFeeUpdated event
5. Should allow owner to update payment limits
6. Should validate min less than max
7. Should emit PaymentLimitsUpdated event
8. Should allow owner to update timeout
9. Should validate timeout bounds (5min - 2hours)
10. Should emit PaymentTimeoutUpdated event
11. Should allow owner to toggle payments
12. Should emit PaymentsToggled event
13. Should revert non-owner updates
14. Should return current configuration correctly

---

## Integration Testing

### End-to-End Payment Flows

**Native Token Payment Flow:**
```javascript
describe("Complete Payment Flow", function() {
  it("Should complete full payment from customer to merchant", async function() {
    // 1. Whitelist merchant
    await merchantRegistry.whitelistMerchant(merchant.address, "Test Store");
    
    // 2. Setup price oracle
    await mockOracle.setPrice(250000000000); // $2500 ETH
    
    // 3. Process payment
    const tx = await paymentProcessor.connect(customer).processPayment(
      merchant.address,
      5000, // 50.00 EUR
      "ORDER123",
      { value: ethers.parseEther("0.02") }
    );
    
    // 4. Verify merchant received payment
    const merchantBalance = await ethers.provider.getBalance(merchant.address);
    expect(merchantBalance).to.be.gt(initialBalance);
    
    // 5. Verify fee collector received fee
    const feeBalance = await ethers.provider.getBalance(feeCollector.address);
    expect(feeBalance).to.equal(ethers.parseEther("0.0002")); // 1% fee
    
    // 6. Verify event emitted
    await expect(tx).to.emit(paymentProcessor, "PaymentCompleted");
    
    // 7. Verify payment stored
    const payment = await paymentProcessor.getPayment(paymentId);
    expect(payment.status).to.equal(1); // Completed
  });
});
```

**Token Payment Flow:**
```javascript
describe("Token Payment Flow", function() {
  it("Should complete USDC payment from customer to merchant", async function() {
    // 1. Whitelist merchant
    // 2. Whitelist USDC token
    // 3. Mint USDC to customer
    // 4. Customer approves TokenPaymentProcessor
    // 5. Process token payment
    // 6. Verify merchant received tokens
    // 7. Verify fee collector received token fee
    // 8. Verify event emitted
    // 9. Verify payment stored
  });
});
```

### Multi-Contract Interactions

**Test: Payment with Oracle Failover**

Scenario: Primary oracle fails, fallback oracle used
```javascript
it("Should use fallback oracle when primary fails", async function() {
  // Setup: Primary oracle returns stale data
  await mockPriceOracle.setStalePrice();
  
  // Setup: Fallback oracle has valid price
  await fallbackOracle.setPrice(250000000000);
  
  // Execute: Process payment
  const tx = await paymentProcessor.processPayment(...);
  
  // Verify: Payment used fallback price
  // Verify: Payment successful
});
```

**Test: Merchant Dewhitelisting Mid-Payment**

Scenario: Merchant is removed after payment initiated but before confirmation
```javascript
it("Should handle merchant dewhitelisting gracefully", async function() {
  // Not possible to test in synchronous blockchain
  // Document behavior: Transaction will succeed or fail atomically
  // If merchant whitelisted at tx start, payment succeeds
});
```

**Test: Multiple Token Types**

Scenario: Same merchant receives payments in different tokens
```javascript
it("Should handle multiple token payments to same merchant", async function() {
  // Process USDC payment
  // Process USDT payment
  // Process DAI payment
  // Verify separate tracking per token
  // Verify statistics correct
});
```

### Cross-Chain Testing

**Test: Same Payment ID Across Chains**
```javascript
it("Should generate unique payment IDs even with same parameters", async function() {
  // Deploy on simulated Ethereum
  // Deploy on simulated BSC
  // Use same merchant, customer, amount, orderId
  // Verify different payment IDs due to timestamp/block difference
});
```

**Test: Multi-Chain Fee Collection**
```javascript
it("Should collect fees independently on each chain", async function() {
  // Setup FeeCollector on multiple chains
  // Process payments on each chain
  // Verify fee collection independent
  // Verify withdrawal per chain
});
```

---

## Security Testing

### Reentrancy Attack Simulations

**Malicious Merchant Contract:**
```solidity
contract MaliciousMerchant {
    PaymentProcessor public processor;
    uint256 public attackCount;
    
    receive() external payable {
        if (attackCount < 5) {
            attackCount++;
            processor.processPayment{value: msg.value}(
                address(this),
                1000,
                "ATTACK"
            );
        }
    }
    
    function attack() external payable {
        processor.processPayment{value: msg.value}(
            address(this),
            1000,
            "ATTACK"
        );
    }
}
```

**Test Cases:**

1. Reentrancy via receive function should revert
2. Reentrancy via fallback function should revert
3. Cross-function reentrancy should revert
4. Nested reentrancy should revert
5. Reentrancy after state changes should not affect state

### Access Control Testing

**Test: Unauthorized Administrative Access**
```javascript
describe("Access Control", function() {
  it("Should prevent non-owner from pausing", async function() {
    await expect(
      paymentProcessor.connect(attacker).pause()
    ).to.be.revertedWith("Ownable: caller is not the owner");
  });
  
  it("Should prevent non-owner from updating oracle", async function() {
    await expect(
      paymentProcessor.connect(attacker).updatePriceOracle(attacker.address)
    ).to.be.revertedWith("Ownable: caller is not the owner");
  });
  
  it("Should prevent non-owner from whitelisting merchants", async function() {
    await expect(
      merchantRegistry.connect(attacker).whitelistMerchant(attacker.address, "Attacker")
    ).to.be.revertedWith("Ownable: caller is not the owner");
  });
});
```

### Oracle Manipulation Testing

**Test: Price Manipulation Attempts**
```javascript
describe("Oracle Security", function() {
  it("Should reject zero price", async function() {
    await mockOracle.setPrice(0);
    await expect(
      paymentProcessor.processPayment(...)
    ).to.be.revertedWith("Invalid price");
  });
  
  it("Should reject stale price", async function() {
    await mockOracle.setPriceWithTimestamp(250000000000, oldTimestamp);
    await expect(
      paymentProcessor.processPayment(...)
    ).to.be.revertedWith("Price data stale");
  });
  
  it("Should enforce price tolerance limits", async function() {
    // Set oracle price
    await mockOracle.setPrice(250000000000);
    
    // Send payment with 10% less (outside 2% tolerance)
    await expect(
      paymentProcessor.processPayment(
        merchant.address,
        5000,
        "ORDER",
        { value: ethers.parseEther("0.018") } // 10% less
      )
    ).to.be.revertedWith("Payment amount mismatch");
  });
});
```

### Rate Limiting Testing
```javascript
describe("Rate Limiting", function() {
  it("Should allow one payment per address per block", async function() {
    // First payment succeeds
    await paymentProcessor.connect(customer).processPayment(...);
    
    // Second payment in same block should revert
    await expect(
      paymentProcessor.connect(customer).processPayment(...)
    ).to.be.revertedWith("Rate limit: one payment per block");
  });
  
  it("Should allow payment in next block", async function() {
    // First payment
    await paymentProcessor.connect(customer).processPayment(...);
    
    // Mine new block
    await ethers.provider.send("evm_mine");
    
    // Second payment succeeds
    await expect(
      paymentProcessor.connect(customer).processPayment(...)
    ).to.not.be.reverted;
  });
});
```

### Gas Griefing Testing
```javascript
describe("Gas Griefing Protection", function() {
  it("Should handle merchant receive failure gracefully", async function() {
    // Deploy merchant contract that always reverts
    const maliciousMerchant = await MaliciousMerchant.deploy();
    
    // Whitelist malicious merchant
    await merchantRegistry.whitelistMerchant(maliciousMerchant.address, "Bad");
    
    // Payment should still succeed even if merchant reverts
    // (Alternative: payment fails but doesn't consume excessive gas)
    const tx = await paymentProcessor.processPayment(
      maliciousMerchant.address,
      1000,
      "ORDER",
      { value: ethers.parseEther("0.01") }
    );
    
    // Verify gas usage is bounded
    const receipt = await tx.wait();
    expect(receipt.gasUsed).to.be.lt(200000);
  });
});
```

### Edge Case Testing

**Boundary Conditions:**

1. Minimum payment amount (1 cent)
2. Maximum payment amount (1 million EUR)
3. Zero merchant address
4. Zero token address
5. Zero amount
6. Maximum uint256 amount
7. Empty order ID string
8. Very long order ID string (max length)
9. All zeros payment ID collision attempt
10. Concurrent payments from same address

**Token Edge Cases:**

1. Token with 0 decimals
2. Token with 6 decimals (USDC)
3. Token with 8 decimals (WBTC)
4. Token with 18 decimals (standard)
5. Token with 27 decimals (rare)
6. Token that reverts on transfer
7. Token that returns false on transfer
8. Token with transfer fees
9. Rebasing tokens
10. Tokens with blacklist functionality

---

## Performance Testing

### Gas Optimization Verification

**Baseline Gas Costs:**

Target gas costs per operation:
```
PaymentProcessor.processPayment: <150,000 gas
TokenPaymentProcessor.processTokenPayment: <180,000 gas
FeeCollector.withdrawAll (2 owners): <100,000 gas
MerchantRegistry.whitelistMerchant: <80,000 gas
TokenWhitelist.addToken: <80,000 gas
```

**Gas Testing:**
```javascript
describe("Gas Optimization", function() {
  it("Should process payment within gas limit", async function() {
    const tx = await paymentProcessor.processPayment(...);
    const receipt = await tx.wait();
    
    console.log(`Gas used: ${receipt.gasUsed}`);
    expect(receipt.gasUsed).to.be.lt(150000);
  });
  
  it("Should optimize fee distribution", async function() {
    // Send fees to collector
    await paymentProcessor.processPayment(...);
    
    // Withdraw fees
    const tx = await feeCollector.withdrawAll();
    const receipt = await tx.wait();
    
    console.log(`Gas used: ${receipt.gasUsed}`);
    expect(receipt.gasUsed).to.be.lt(100000);
  });
});
```

### Load Testing

**Concurrent Payment Simulation:**
```javascript
describe("Load Testing", function() {
  it("Should handle 100 concurrent payments", async function() {
    const promises = [];
    
    for (let i = 0; i < 100; i++) {
      const customer = customers[i];
      promises.push(
        paymentProcessor.connect(customer).processPayment(
          merchant.address,
          1000 + i,
          `ORDER${i}`,
          { value: ethers.parseEther("0.01") }
        )
      );
    }
    
    // All should succeed
    await Promise.all(promises);
    
    // Verify all payments recorded
    const merchantStats = await paymentProcessor.getMerchantStats(merchant.address);
    expect(merchantStats.totalReceived).to.be.gt(0);
  });
});
```

### Stress Testing

**High-Frequency Merchant:**
```javascript
it("Should handle merchant with 1000 payments", async function() {
  for (let i = 0; i < 1000; i++) {
    await paymentProcessor.connect(customers[i % 10]).processPayment(
      merchant.address,
      1000,
      `ORDER${i}`,
      { value: ethers.parseEther("0.01") }
    );
  }
  
  // Verify statistics correct
  const stats = await paymentProcessor.getMerchantStats(merchant.address);
  expect(stats.totalReceived).to.equal(ethers.parseEther("9.9")); // 1000 * 0.01 * 0.99 (after 1% fee)
});
```

---

## Test Coverage Requirements

### Minimum Coverage Targets

**Overall Target: 95%+**

Per contract requirements:
```
PaymentProcessor:
- Branch coverage: 95%
- Statement coverage: 98%
- Function coverage: 100%
- Line coverage: 97%

TokenPaymentProcessor:
- Branch coverage: 95%
- Statement coverage: 98%
- Function coverage: 100%
- Line coverage: 97%

PriceOracle:
- Branch coverage: 90%
- Statement coverage: 95%
- Function coverage: 100%
- Line coverage: 95%

FallbackPriceOracle:
- Branch coverage: 90%
- Statement coverage: 95%
- Function coverage: 100%
- Line coverage: 95%

MerchantRegistry:
- Branch coverage: 95%
- Statement coverage: 98%
- Function coverage: 100%
- Line coverage: 97%

TokenWhitelist:
- Branch coverage: 95%
- Statement coverage: 98%
- Function coverage: 100%
- Line coverage: 97%

FeeCollector:
- Branch coverage: 95%
- Statement coverage: 98%
- Function coverage: 100%
- Line coverage: 97%

ProtocolConfig:
- Branch coverage: 90%
- Statement coverage: 95%
- Function coverage: 100%
- Line coverage: 95%
```

### Coverage Exclusions

Acceptable exclusions from coverage:

1. Constructor parameter validation (if same as function validation)
2. Unreachable code paths (provably impossible)
3. Explicit revert statements already tested
4. View functions with single return statement
5. Events (if emission tested elsewhere)

### Coverage Reporting
```bash
# Generate coverage report
npm run coverage

# View HTML report
open coverage/index.html

# CI/CD integration
npm run coverage:ci
```

---

## Testing Tools

### Core Testing Framework

**Hardhat:**
- Local blockchain simulation
- Fast test execution
- Built-in debugging
- Gas reporting

**Mocha:**
- Test structure and organization
- Describe/it syntax
- Before/after hooks
- Test skipping and focusing

**Chai:**
- Assertion library
- Expect syntax
- Custom matchers
- Error message testing

### Security Testing Tools

**Slither:**
- Static analysis
- Vulnerability detection
- Code optimization suggestions
```bash
# Run Slither analysis
slither contracts/ --print human-summary

# Detect specific issues
slither contracts/ --detect reentrancy-eth
slither contracts/ --detect unprotected-upgrade
```

**Mythril:**
- Symbolic execution
- Deep security analysis
- False positive filtering
```bash
# Analyze contract
myth analyze contracts/PaymentProcessor.sol
```

**Echidna:**
- Fuzzing tool
- Property-based testing
- Edge case discovery

### Coverage Tools

**Solidity Coverage:**
- Line coverage
- Branch coverage
- Function coverage
- Statement coverage
```bash
# Generate coverage
npx hardhat coverage

# Coverage threshold enforcement
npx hardhat coverage --check-coverage --lines 95 --functions 100
```

### Gas Profiling

**Hardhat Gas Reporter:**
- Gas cost per function
- Deployment costs
- Method comparison
- Historical tracking

Configuration:
```javascript
// hardhat.config.js
gasReporter: {
  enabled: true,
  currency: 'EUR',
  gasPrice: 50,
  coinmarketcap: process.env.COINMARKETCAP_API_KEY
}
```

### Continuous Integration

**GitHub Actions:**
```yaml
name: Tests

on: [push, pull_request]

jobs:
  test:
    runs-on: ubuntu-latest
    steps:
      - uses: actions/checkout@v2
      - uses: actions/setup-node@v2
      - run: npm install
      - run: npm run compile
      - run: npm run test
      - run: npm run coverage
      - run: npm run lint
```

---

## Related Documentation

**Architecture:**
- [System Overview](../architecture/OVERVIEW.md) - High-level architecture
- [Smart Contracts](../architecture/SMART-CONTRACTS.md) - Contract specifications
- [Security](../architecture/SECURITY.md) - Security mechanisms

**Development:**
- [Development Timeline](./TIMELINE.md) - Project roadmap
- [Deployment Guide](./DEPLOYMENTS.md) - Deployment procedures

---

[Back to Documentation Index](../README.md) | [Main Repository](../../README.md)

---

**Last Updated:** November 2025  
**Version:** 1.0.0
