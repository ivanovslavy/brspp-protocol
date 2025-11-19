# System Architecture Overview

**High-Level Architecture and Design Principles**

[Documentation Index](../README.md) | [Smart Contracts](./SMART-CONTRACTS.md) | [Security](./SECURITY.md)

---

## Table of Contents

1. [Architecture Principles](#architecture-principles)
2. [System Components](#system-components)
3. [Payment Flow](#payment-flow)
4. [Multi-Chain Architecture](#multi-chain-architecture)
5. [Integration Points](#integration-points)

---

## Architecture Principles

### Trustless Design

The protocol eliminates trust requirements through on-chain execution of all critical operations. Payments flow directly from customer wallets to merchant wallets without intermediary custody. Smart contracts enforce business logic immutably, removing reliance on centralized infrastructure.

### Deterministic Execution

All payment processing logic executes deterministically on-chain. Price conversions use Chainlink oracles with fallback mechanisms. Fee calculations follow fixed algorithms verifiable by any party. Transaction outcomes are predictable and auditable.

### Defense in Depth

Security operates at multiple layers: smart contract level (reentrancy guards, access controls), protocol level (rate limiting, whitelists), and operational level (multi-signature controls, emergency pauses). Each layer provides independent protection against different attack vectors.

### Upgradeable Parameters

While core contract logic remains immutable, protocol parameters can be updated through governance mechanisms. This allows fee adjustments, oracle updates, and configuration changes without requiring contract redeployment or merchant migration.

---

## System Components

### Layer 1: Smart Contracts

Eight core contracts deployed identically across all supported networks.

**Payment Processing**
- PaymentProcessor handles native token payments (ETH, BNB, MATIC)
- TokenPaymentProcessor handles ERC20 token payments (USDC, USDT, DAI)

**Price Oracles**
- PriceOracle provides primary Chainlink price feed integration
- FallbackPriceOracle offers redundancy for high availability

**Access Control**
- MerchantRegistry maintains whitelist of authorized merchants
- TokenWhitelist defines approved payment tokens

**Fee Management**
- FeeCollector distributes fees to protocol owners using configurable percentage splits
- ProtocolConfig stores updatable protocol parameters

[Detailed Contract Specifications](./SMART-CONTRACTS.md)

### Layer 2: Backend Infrastructure

Node.js backend provides off-chain services complementing on-chain protocol.

**API Services**
- RESTful API for merchant onboarding and management
- Payment initiation and status tracking
- Webhook delivery for payment notifications

**Event Monitoring**
- Blockchain event listeners for all supported networks
- Real-time payment status updates
- Transaction confirmation tracking

**Data Persistence**
- PostgreSQL database for merchant accounts, payment records, and analytics
- Redis cache for high-frequency data access
- Encrypted credential storage for sensitive merchant data

[Backend Documentation](https://github.com/ivanovslavy/brspp-platform)

### Layer 3: Frontend Interface

React-based user interfaces for merchants and customers.

**Merchant Dashboard**
- Account management and API key generation
- Payment history and analytics
- Settlement tracking and reporting

**Customer Checkout**
- Multi-chain payment widget
- Wallet connection interface
- Real-time transaction status

[Frontend Documentation](https://github.com/ivanovslavy/brspp-platform)

---

## Payment Flow

### Native Token Payment Flow
```
1. Customer initiates payment
   - Visits merchant checkout page
   - Selects cryptocurrency payment option
   - Amount displayed in EUR/USD with crypto equivalent

2. Payment page loads
   - Backend creates payment record
   - Generates unique payment ID
   - Calculates crypto amount using oracle price

3. Customer connects wallet
   - MetaMask or compatible wallet
   - Switches to correct network if needed
   - Reviews transaction details

4. Smart contract interaction
   - Customer approves transaction
   - PaymentProcessor.processPayment() called
   - Contract validates merchant whitelist status
   - Contract calculates and deducts 1% protocol fee
   - Remaining amount transferred to merchant
   - Fee transferred to FeeCollector

5. Confirmation
   - Transaction mined on blockchain
   - Event emitted: PaymentCompleted
   - Backend listener captures event
   - Payment status updated to completed
   - Webhook sent to merchant
   - Customer redirected to success page
```

### Token Payment Flow
```
1-3. Same as native token flow

4. Token approval (if needed)
   - Customer approves TokenPaymentProcessor to spend tokens
   - Separate transaction for first-time token usage

5. Smart contract interaction
   - TokenPaymentProcessor.processTokenPayment() called
   - Contract validates token is whitelisted
   - Contract validates merchant whitelist status
   - Contract calculates fee based on token amount
   - Tokens transferred from customer to merchant
   - Fee tokens transferred to FeeCollector

6. Confirmation
   - Same as native token flow
```

[Detailed Payment Flows in Smart Contract Specs](./SMART-CONTRACTS.md)

---

## Multi-Chain Architecture

### Identical Deployment Strategy

All eight core contracts deploy with identical code across Ethereum, BSC, and Polygon. This ensures consistent behavior regardless of network choice.

**Benefits:**
- Unified integration for merchants (same ABI across chains)
- Consistent security model across all networks
- Simplified testing and auditing
- Reduced development complexity

**Network-Specific Considerations:**
- Gas costs vary significantly (Ethereum > BSC > Polygon)
- Block times differ (affects confirmation speed)
- Oracle addresses differ per network
- Native tokens differ (ETH vs BNB vs MATIC)

### Network Selection

Customers choose payment network based on preferences:

**Ethereum:**
- Highest security and decentralization
- Highest gas costs
- Best for high-value transactions

**Binance Smart Chain:**
- Lower gas costs than Ethereum
- Faster block times
- Good for medium-value transactions

**Polygon:**
- Lowest gas costs
- Fastest confirmation
- Optimal for frequent small transactions

Merchants receive payments on the same network customers use. Cross-chain bridging is not part of the protocol scope.

---

## Integration Points

### Merchant Integration

Merchants integrate BRSPP through multiple methods:

**Direct Smart Contract Integration**
- Advanced developers can call contracts directly
- Requires blockchain development expertise
- Full control over payment flow

**Backend API Integration**
- RESTful API abstracts blockchain complexity
- Suitable for traditional web developers
- Handles wallet connections and transaction submission

**WordPress Plugin**
- One-click installation for WooCommerce
- No coding required
- Automatic payment page generation

**Payment Widget**
- Embeddable JavaScript widget
- Minimal integration effort
- Customizable appearance

[Integration Guides in Platform Repo](https://github.com/ivanovslavy/brspp-platform)

### Oracle Integration

Protocol depends on Chainlink price feeds for accurate EUR/USD to crypto conversions.

**Primary Oracle (PriceOracle.sol):**
- Queries Chainlink aggregator for latest price
- Validates price freshness (staleness check)
- Returns price with 8 decimal precision

**Fallback Oracle (FallbackPriceOracle.sol):**
- Secondary price source if primary fails
- Can use alternative oracle providers
- Activated automatically if primary is unavailable

**Supported Price Pairs:**
- ETH/USD (Ethereum)
- BNB/USD (BSC)
- MATIC/USD (Polygon)
- USDC/USD (all chains)
- USDT/USD (all chains)
- DAI/USD (all chains)

[Oracle Implementation Details](./SMART-CONTRACTS.md)

### Fee Collection Integration

FeeCollector contract supports multi-owner fee distribution.

**Configuration:**
- Owners: array of Ethereum addresses
- Percentages: corresponding percentage allocations
- Total must equal 10000 (100.00%)

**Example:**
```
Owner 1: 70% (7000 basis points)
Owner 2: 30% (3000 basis points)
```

**Withdrawal:**
- Any owner can trigger withdrawAll()
- Contract automatically distributes to all owners
- No manual calculation needed
- Transparent on-chain record

[FeeCollector Specification](./SMART-CONTRACTS.md)

---

## Security Architecture

### Smart Contract Security

Multiple protection layers at contract level:

**Reentrancy Protection:**
- ReentrancyGuard on all state-changing functions
- Checks-effects-interactions pattern
- No external calls before state updates

**Access Controls:**
- Ownable pattern for administrative functions
- Whitelist checks before processing
- Role-based permissions where needed

**Rate Limiting:**
- Per-address payment limits per block
- Prevents spam and DOS attacks
- Configurable via ProtocolConfig

**Input Validation:**
- Amount bounds checking
- Address validation (non-zero)
- Overflow protection (Solidity 0.8+)

**Emergency Controls:**
- Pausable functionality for critical issues
- Multi-signature requirement for sensitive operations
- Time-locks on parameter updates

[Complete Security Analysis](./SECURITY.md)

### Operational Security

Security extends beyond smart contracts:

**Infrastructure:**
- Encrypted database credentials
- API rate limiting
- DDoS protection
- Regular security updates

**Access Management:**
- Multi-factor authentication for admin access
- API key rotation policies
- Least-privilege access controls

**Monitoring:**
- Real-time transaction monitoring
- Anomaly detection
- Automated alerting for suspicious activity

[Operational Security Details](./SECURITY.md)

---

## Performance Considerations

### Transaction Throughput

Protocol throughput is limited by underlying blockchain capacity:

**Ethereum:**
- 15-30 transactions per second network-wide
- Higher gas costs during congestion
- Suitable for high-value, lower-frequency payments

**BSC:**
- 50-100 transactions per second
- More consistent gas costs
- Good balance of speed and cost

**Polygon:**
- 7000+ transactions per second theoretical
- Very low gas costs
- Excellent for high-frequency small payments

Protocol itself adds minimal overhead (single contract call per payment).

### Scalability Strategy

**Horizontal Scaling:**
- Multi-chain deployment distributes load
- Merchants can choose optimal network per use case
- Future additional chain support (Avalanche, Arbitrum, etc.)

**Vertical Optimization:**
- Gas-optimized contract code
- Minimal storage operations
- Efficient event logging

**Layer 2 Integration (Future):**
- Potential Optimism/Arbitrum deployment
- Significantly lower costs
- Maintained security guarantees

---

## Monitoring and Observability

### On-Chain Monitoring

All protocol activity is transparent on-chain:

**Events:**
- PaymentCompleted (successful payments)
- PaymentFailed (failed attempts)
- MerchantWhitelisted (merchant approval)
- FeeWithdrawn (fee collection events)

**Block Explorers:**
- Etherscan (Ethereum)
- BscScan (BSC)
- PolygonScan (Polygon)

All events are indexed and searchable.

### Off-Chain Monitoring

Backend services provide additional observability:

**Metrics:**
- Payment volume per network
- Success/failure rates
- Average transaction values
- Merchant activity statistics

**Logs:**
- API request/response logs
- Event listener activity
- Webhook delivery status
- Error tracking

**Dashboards:**
- Real-time payment monitoring
- Network health indicators
- Protocol utilization metrics

---

## Related Documentation

**Technical Details:**
- [Smart Contract Specifications](./SMART-CONTRACTS.md) - Complete contract documentation
- [Security Mechanisms](./SECURITY.md) - Security features and threat models

**Development:**
- [Development Timeline](../development/TIMELINE.md) - Project roadmap and milestones
- [Testing Strategy](../development/TESTING.md) - Test coverage and methodology
- [Deployment Guide](../development/DEPLOYMENTS.md) - Deployment procedures

**Business:**
- [Corporate Structure](../legal/STRUCTURE.md) - Business entity strategy
- [Compliance Requirements](../legal/COMPLIANCE.md) - Regulatory considerations

---

[Back to Documentation Index](../README.md) | [Main Repository](../../README.md)

---

**Last Updated:** November 2025  
**Version:** 1.0.0
