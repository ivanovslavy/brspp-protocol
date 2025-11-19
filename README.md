# BRSPP Protocol

**BRS Payment Processor - Decentralized Multi-Chain Payment Gateway Protocol**

Version 1.0.0 | [Documentation](./docs/README.md) | [Platform Repository](https://github.com/ivanovslavy/brspp-platform)

---

## Overview

BRSPP is a trustless payment gateway protocol that enables merchants to accept cryptocurrency payments directly from customers without custodial intermediaries. The protocol operates across Ethereum, Binance Smart Chain, and Polygon networks with a transparent 1% fee structure enforced by immutable smart contracts.

### Core Features

**Trustless Architecture**  
Payments flow directly from customer wallets to merchant wallets via smart contracts. The protocol never holds custody of funds at any point in the transaction lifecycle.

**Multi-Chain Support**  
Native deployment on three major EVM-compatible networks enables merchants to accept payments across multiple blockchains with unified integration.

**Transparent Fees**  
Fixed 1% protocol fee collected automatically on-chain. Fee distribution to protocol owners is managed by multi-signature smart contracts with configurable percentage splits.

**Non-Custodial Design**  
All payment routing, validation, and fee collection occurs on-chain through audited smart contracts, eliminating reliance on centralized infrastructure.

---

## Repository Structure
```
brspp-protocol/
├── contracts/              Smart contract source code (Solidity)
├── scripts/                Deployment and interaction scripts
├── test/                   Comprehensive test suite
├── docs/                   Technical documentation
├── abi/                    Exported contract ABIs (for integrations)
├── hardhat.config.js       Hardhat configuration
└── package.json            Project dependencies
```

---

## Smart Contract Architecture

The protocol consists of eight core contracts deployed identically across all supported networks:

### Payment Processing Layer
- **PaymentProcessor.sol** - Handles native token payments (ETH/BNB/MATIC)
- **TokenPaymentProcessor.sol** - Processes ERC20 token payments (USDC, USDT, DAI)

### Oracle Integration
- **PriceOracle.sol** - Primary Chainlink oracle integration for real-time price feeds
- **FallbackPriceOracle.sol** - Redundant oracle system for high availability

### Access Control
- **MerchantRegistry.sol** - Whitelist management for authorized merchants
- **TokenWhitelist.sol** - Registry of approved payment tokens

### Fee Management
- **FeeCollector.sol** - Multi-owner fee distribution with percentage-based allocation
- **ProtocolConfig.sol** - Upgradeable protocol parameters and settings

[Detailed Architecture Documentation](./docs/architecture/OVERVIEW.md)

---

## Supported Networks

### Mainnet Deployments
- **Ethereum** - Coming Q2 2026
- **Binance Smart Chain** - Coming Q2 2026
- **Polygon** - Coming Q2 2026

### Testnet Deployments
- **Sepolia** (Ethereum testnet) - Active
- **BSC Testnet** - Active
- **Polygon Amoy** - Active

Contract addresses and deployment details available in deployment documentation.

---

## Technology Stack

**Smart Contracts**
- Solidity 0.8.20
- OpenZeppelin Contracts (security-audited libraries)
- Chainlink Price Feeds (decentralized oracles)

**Development Tools**
- Hardhat (development environment)
- Ethers.js v6 (blockchain interaction)
- Slither (static analysis)
- Hardhat Gas Reporter (optimization)

**Testing Framework**
- Chai (assertions)
- Mocha (test runner)
- Hardhat Network (local blockchain)

---

## Quick Start

### Prerequisites

- Node.js v18 or higher
- npm or yarn package manager
- MetaMask or similar Web3 wallet

### Installation
```bash
# Clone repository
git clone https://github.com/ivanovslavy/brspp-protocol.git
cd brspp-protocol

# Install dependencies
npm install

# Configure environment
cp .env.example .env
# Edit .env with your RPC URLs and private keys
```

### Compile Contracts
```bash
npm run compile
```

### Run Tests
```bash
# Run full test suite
npm test

# Run with gas reporting
npm run test:gas

# Run with coverage
npm run coverage
```

### Deploy to Testnet
```bash
# Deploy to Sepolia
npm run deploy:sepolia

# Deploy to BSC Testnet
npm run deploy:bsc-testnet

# Deploy to Polygon Amoy
npm run deploy:amoy
```

---

## Documentation

Comprehensive technical documentation is available in the /docs directory:

### Architecture Documentation
- [System Overview](./docs/architecture/OVERVIEW.md) - High-level architecture and design principles
- [Smart Contract Specifications](./docs/architecture/SMART-CONTRACTS.md) - Detailed contract documentation
- [Security Mechanisms](./docs/architecture/SECURITY.md) - Security features and threat mitigation

### Development Documentation
- [Development Timeline](./docs/development/TIMELINE.md) - 15-month roadmap and milestones
- [Testing Strategy](./docs/development/TESTING.md) - Test coverage and methodology
- [Deployment Guide](./docs/development/DEPLOYMENTS.md) - Network deployment procedures

### Legal and Compliance
- [Corporate Structure](./docs/legal/STRUCTURE.md) - Business entity strategy and jurisdiction analysis
- [Compliance Requirements](./docs/legal/COMPLIANCE.md) - Regulatory considerations and licensing

### Future Development
- [Fiat Payment Integration](./docs/future/FIAT-INTEGRATION.md) - Stripe/PayPal expansion roadmap
- [Governance System](./docs/future/GOVERNANCE.md) - Community-driven protocol upgrades

[Full Documentation Index](./docs/README.md)

---

## Security

Security is the highest priority for BRSPP. Multiple layers of protection ensure safe operation:

### Smart Contract Security
- Reentrancy protection (ReentrancyGuard pattern)
- Access control (Ownable, role-based permissions)
- Rate limiting (prevents denial-of-service attacks)
- Emergency pause mechanisms
- Comprehensive input validation

### Audit Process
- Internal security review using Slither static analysis
- Comprehensive test suite with 95%+ coverage target
- External audit by reputable security firm (planned)
- Bug bounty program post-mainnet launch

### Vulnerability Reporting

Security researchers who discover vulnerabilities should report them privately to:

**Email:** security@brspp.com  
**PGP Key:** Available on request

Responsible disclosure policy applies. Public disclosure should occur only after vulnerability has been addressed.

[Detailed Security Documentation](./docs/architecture/SECURITY.md)

---

## Integration

### For Developers

To integrate BRSPP into your application:

1. Install the BRSPP SDK (coming soon) or use contract ABIs directly
2. Initialize contract instances with published addresses
3. Implement payment flow using protocol methods
4. Handle webhook notifications for payment confirmation

Example integration:
```javascript
const { ethers } = require('ethers');
const PaymentProcessorABI = require('./abi/PaymentProcessor.json');

const processor = new ethers.Contract(
  PAYMENT_PROCESSOR_ADDRESS,
  PaymentProcessorABI,
  signer
);

// Create payment
await processor.processPayment(
  merchantAddress,
  amountInWei,
  orderId,
  { value: amountInWei }
);
```

Detailed integration guides and API documentation available in [platform repository](https://github.com/ivanovslavy/brspp-platform).

---

## Platform Components

This repository contains only the smart contract layer. The complete BRSPP ecosystem includes:

**Protocol Layer (this repository)**  
Smart contracts, deployment scripts, and protocol documentation.

**Platform Layer** ([brspp-platform](https://github.com/ivanovslavy/brspp-platform))  
Backend API, merchant dashboard, customer checkout interface, and integration SDKs.

Both components work together to provide a complete payment solution.

---

## Contributing

BRSPP will transition to open-source community development after initial audit and mainnet launch.

### Current Status
The protocol is under active development. Smart contracts are public for transparency but contributions are limited during the security audit phase.

### Future Contributions
After mainnet launch, community contributions will be welcome through:
- Pull requests for protocol improvements
- Bug reports and security disclosures
- Documentation enhancements
- Integration examples and tutorials

Contributing guidelines will be published upon open-source transition.

---

## Development Roadmap

**Phase 1: Foundation** (Months 1-6)  
Smart contract development, security implementation, comprehensive testing suite, and audit preparation.

**Phase 2: Integration** (Months 7-12)  
Backend infrastructure, merchant dashboard, payment widget, testnet deployment and merchant beta program.

**Phase 3: Launch** (Months 13-15)  
External security audit, mainnet deployment, merchant onboarding, and production monitoring.

**Phase 4: Expansion** (Months 24-36)  
Fiat payment integration, additional blockchain networks, and community governance implementation.

Current Status: Phase 1 in progress.

[Detailed Timeline](./docs/development/TIMELINE.md)

---

## License

This project is licensed under the MIT License with additional terms protecting the BRSPP trademark and brand identity.

### Key Terms
- Permission to use, modify, and distribute for any purpose
- BRSPP name and brand assets may not be used without permission
- Forks must use clearly different branding
- No warranty provided

See [LICENSE](./LICENSE) file for complete terms.

---

## Disclaimer

BRSPP is a software protocol providing payment infrastructure. We are not a financial institution, payment processor, or money transmitter. Merchants using the protocol are responsible for compliance with applicable regulations in their jurisdiction.

This software is experimental and provided "as is" without warranty. Use in production environments is at your own risk. Always conduct independent security review before handling real funds.

---

## Contact

**Developer**  
Blockchain Research Systems (BRS Technology EOOD)

**Repository**  
https://github.com/ivanovslavy/brspp-protocol

**Platform Repository**  
https://github.com/ivanovslavy/brspp-platform

**Website**  
https://brspp.slavy.space (coming soon)

**Documentation**  
https://docs.brspp.com (coming soon)

**Security Issues**  
security@brspp.com

---

## Acknowledgments

Built with:
- OpenZeppelin secure smart contract libraries
- Chainlink decentralized oracle network
- Hardhat Ethereum development environment
- Ethereum and EVM-compatible blockchain infrastructure

---

**Last Updated:** November 2025  
**Version:** 1.0.0-dev  
**Status:** Active Development
