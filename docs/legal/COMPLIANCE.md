# Compliance Requirements

**Regulatory Considerations and Licensing**

[Documentation Index](../README.md) | [Corporate Structure](./STRUCTURE.md)

---

## Table of Contents

1. [Regulatory Overview](#regulatory-overview)
2. [Payment Services Regulation](#payment-services-regulation)
3. [Data Protection and Privacy](#data-protection-and-privacy)
4. [Anti-Money Laundering](#anti-money-laundering)
5. [Terms of Service Framework](#terms-of-service-framework)
6. [Merchant Agreements](#merchant-agreements)
7. [Compliance Monitoring](#compliance-monitoring)

---

## Regulatory Overview

### Classification Challenge

**Key Question:** What type of entity is BRSPP from a regulatory perspective?

**Not a Payment Institution because:**
- Does not hold custody of funds at any point
- Does not process fiat currency transactions (crypto only during Phase 1)
- Does not convert fiat to crypto or vice versa
- Does not provide payment accounts
- Does not issue electronic money

**Not a VASP (Virtual Asset Service Provider) because:**
- Does not provide exchange services
- Does not offer wallet services with custody
- Does not transfer virtual assets on behalf of customers
- Smart contracts execute transfers directly peer-to-peer

**Classification: Software Provider**
- Provides technology infrastructure
- Licenses software platform to merchants
- Facilitates transactions but does not process them
- Non-custodial architecture

### EU Regulatory Framework

**Key Regulations Affecting BRSPP:**

**1. Markets in Crypto-Assets Regulation (MiCA)**
- Effective: 2024-2025 (phased implementation)
- Scope: Crypto-asset service providers (CASPs)
- Impact on BRSPP: Likely minimal (non-custodial software)

**2. Payment Services Directive 2 (PSD2)**
- Scope: Payment institutions, e-money institutions
- Impact on BRSPP: Not applicable (no fiat processing in Phase 1)

**3. General Data Protection Regulation (GDPR)**
- Scope: All entities processing personal data in EU
- Impact on BRSPP: Full compliance required

**4. Anti-Money Laundering Directive (AMLD5/AMLD6)**
- Scope: Financial institutions, some crypto service providers
- Impact on BRSPP: Risk-based approach required

**5. Electronic Commerce Directive**
- Scope: Online service providers
- Impact on BRSPP: Applies to website and platform

### MiCA Regulation Analysis

**What MiCA Regulates:**

Crypto-Asset Service Provider (CASP) activities:
- Operating a trading platform for crypto-assets
- Exchanging crypto-assets for fiat currency or other crypto-assets
- Executing orders for crypto-assets on behalf of clients
- Placing crypto-assets
- Providing custody and administration of crypto-assets
- Providing advice on crypto-assets
- Providing portfolio management on crypto-assets
- Receiving and transmitting orders for crypto-assets

**BRSPP Activities Analysis:**
```
BRSPP does:
- Provide software that routes payment information
- Emit smart contract calls based on merchant configuration
- Track payment status off-chain

BRSPP does NOT:
- Hold custody of crypto-assets
- Execute trades or exchanges
- Provide investment advice
- Manage customer portfolios
- Transmit crypto-assets (smart contracts do this)
```

**Conclusion:** BRSPP likely falls outside MiCA scope as pure software infrastructure provider. However, regulatory guidance is evolving.

**Mitigation Strategy:**
- Monitor MiCA implementation closely
- Obtain legal opinion from EU crypto lawyer
- Maintain non-custodial architecture
- Be prepared to register if guidance changes
- Design Terms of Service to clarify role

### National Implementations

**Bulgaria:**
- No specific crypto regulation yet (as of 2025)
- General business laws apply
- MiCA will apply directly as EU regulation
- Banking supervision by Bulgarian National Bank

**Lithuania:**
- Virtual Currency Operator registration exists
- Applies to: exchanges, wallet providers, ICO platforms
- BRSPP assessment: Likely exempt but requires legal review
- If required: Registration with Financial Crime Investigation Service (FCIS)
- Cost: ~3,000 EUR, Timeline: 2-3 months

---

## Payment Services Regulation

### PSD2 and Payment Institutions

**When PSD2 Applies:**

A Payment Institution license is required for:
- Providing payment accounts
- Executing payment transactions
- Money remittance
- Payment initiation services
- Account information services

**BRSPP Analysis:**
```
Does BRSPP provide payment accounts?
NO - Merchants use their own wallets

Does BRSPP execute payment transactions?
NO - Smart contracts execute transactions autonomously

Does BRSPP remit money?
NO - No intermediation of funds transfer

Conclusion: Payment Institution license NOT required
```

**Legal Positioning:**

BRSPP is:
- Software infrastructure provider
- Technology platform
- API service provider

BRSPP is NOT:
- Payment service provider
- Money transmitter
- Payment processor in legal sense

### Fiat Payment Integration (Phase 2)

**When Adding Stripe/PayPal Integration:**

**Challenge:** Does fiat integration trigger licensing requirements?

**Analysis:**
```
Model: Merchant-Direct API Aggregation

Merchants connect their own Stripe/PayPal accounts
→ Stripe/PayPal hold payment institution licenses
→ Stripe/PayPal process payments
→ BRSPP only routes API calls
→ BRSPP never touches fiat funds

Analogy: E-commerce platform (WooCommerce, Shopify)
→ They integrate Stripe/PayPal
→ They don't need payment licenses
→ Same model for BRSPP
```

**Conclusion:** Merchant-direct integration avoids licensing requirements.

**Critical Requirements:**
- Merchants must have direct relationship with Stripe/PayPal
- BRSPP never holds merchant funds
- Clear disclosure in Terms of Service
- Proper data handling (PCI-DSS via Stripe/PayPal, not BRSPP)

### Terms of Service Disclosures

**Required Disclaimers:**
```
Payment Processing:

"BRSPP is a software platform that facilitates payment 
processing between merchants and customers. We do not 
process, hold, or transmit funds directly.

Cryptocurrency Payments:
Payments are executed via smart contracts on public 
blockchains. BRSPP does not custody funds at any time.

Fiat Payments (if applicable):
Fiat payments are processed through third-party payment 
processors (Stripe, PayPal) with which merchants have 
direct accounts. BRSPP acts solely as software 
integration layer.

Regulatory Status:
BRSPP is not a Payment Institution, Electronic Money 
Institution, or Virtual Asset Service Provider. Merchants 
are responsible for compliance with applicable regulations 
in their jurisdiction."
```

---

## Data Protection and Privacy

### GDPR Compliance

**GDPR Applies to BRSPP because:**
- Processes personal data of EU residents
- Established in EU (Bulgaria, then Lithuania)
- Offers services to EU data subjects

**Personal Data Collected:**

**From Merchants:**
- Email address (for account creation)
- Wallet address (for payments)
- Business name (optional)
- API keys (generated, not personal data)
- Payment history (transaction metadata)

**From Customers:**
- Wallet address (visible on blockchain)
- Transaction amounts (visible on blockchain)
- Email address (optional, if merchant collects)
- IP address (server logs)

**Data Processing Principles:**

**1. Lawfulness, Fairness, Transparency**
- Clear privacy policy
- Explicit consent where required
- Transparent data practices

**2. Purpose Limitation**
- Data used only for stated purposes
- Payment processing and protocol operation
- No sale to third parties
- Marketing only with consent

**3. Data Minimization**
- Collect only necessary data
- No KYC documents required from customers
- No unnecessary personal information

**4. Accuracy**
- Merchant can update information
- Data correction procedures in place

**5. Storage Limitation**
- Payment records: 5 years (compliance requirement)
- Account data: Duration of business relationship
- Logs: 90 days
- Anonymization after retention period

**6. Integrity and Confidentiality**
- Encryption at rest (database)
- Encryption in transit (TLS)
- Access controls
- Regular security audits

**7. Accountability**
- Data processing records
- Privacy policy published
- GDPR compliance documentation
- Data Protection Officer (if required)

### Privacy Policy Requirements

**Essential Elements:**

**1. Data Controller Information**
```
Data Controller:
BRS Technology EOOD (Phase 1)
BRS Payment Processor UAB (Phase 2)
Address: [Company Address]
Email: privacy@brspp.com
```

**2. Types of Data Collected**
```
Account Data:
- Email address
- Wallet address
- Business name (optional)

Transaction Data:
- Payment amounts
- Timestamps
- Blockchain transaction hashes
- Merchant and customer wallet addresses

Technical Data:
- IP addresses
- Browser information
- Usage statistics
```

**3. Purpose of Processing**
```
We process data for:
- Account creation and management
- Payment processing facilitation
- Protocol operation and monitoring
- Fraud prevention
- Legal compliance
- Customer support
```

**4. Legal Basis**
```
Contract Performance: 
Processing necessary to provide service

Legitimate Interest:
Fraud prevention, security, analytics

Legal Obligation:
Tax reporting, AML compliance

Consent:
Marketing communications (opt-in)
```

**5. Data Sharing**
```
We share data with:
- Payment processors (Stripe, PayPal) - as necessary
- Blockchain networks (public by nature)
- Service providers (hosting, analytics) - under DPA
- Legal authorities (when required by law)

We do NOT:
- Sell data to third parties
- Use data for unrelated purposes
- Share with advertisers
```

**6. International Transfers**
```
Data may be processed in:
- Bulgaria (Phase 1)
- Lithuania (Phase 2)
- EU cloud providers
- Non-EU: Only with adequate safeguards (SCCs)
```

**7. Data Subject Rights**
```
Users have right to:
- Access their data
- Rectify inaccurate data
- Erase data (right to be forgotten)*
- Restrict processing
- Data portability
- Object to processing
- Withdraw consent

*Note: Blockchain data is immutable and cannot be erased
```

**8. Contact Information**
```
Privacy Officer:
Email: privacy@brspp.com
Response time: 30 days maximum
```

### Cookie Policy

**If Website Uses Cookies:**

**Essential Cookies:**
- Session management
- Authentication
- No consent required

**Analytics Cookies:**
- Google Analytics
- Usage tracking
- Consent required (opt-in banner)

**Marketing Cookies:**
- Advertising
- Retargeting
- Consent required

**Cookie Banner:**
```
Required elements:
- Clear explanation of cookie use
- Granular consent options
- Easy opt-out
- Settings accessible at any time
```

### Data Processing Agreements

**When Required:**

If using third-party processors:
- Cloud hosting providers (AWS, Google Cloud)
- Analytics providers (Google Analytics)
- Email service providers
- Support platforms

**DPA Must Include:**
- Scope of processing
- Purpose and duration
- Type of personal data
- Data subject categories
- Controller and processor obligations
- Sub-processor provisions
- Security measures
- Data breach procedures
- Audit rights

### Blockchain Data Considerations

**Challenge:** Blockchain data is immutable and public

**Solution:**
```
Privacy Policy Disclosure:

"Payment transactions are executed on public blockchains. 
Wallet addresses and transaction amounts are permanently 
recorded on the blockchain and cannot be deleted or 
modified. This is inherent to blockchain technology.

To protect privacy:
- We do not link wallet addresses to personal identities
- Personal data stored off-chain can be deleted
- Users should be aware of blockchain's permanent nature"
```

**Best Practices:**
- Minimize off-chain personal data
- Never publish personal data on-chain
- Use pseudonymous wallet addresses
- Educate users about blockchain transparency

---

## Anti-Money Laundering

### AML/CFT Framework

**EU AML Directives (AMLD5/AMLD6):**

Apply to:
- Financial institutions
- Virtual Asset Service Providers (VASPs)
- Some other entities

**BRSPP Assessment:**
```
Is BRSPP a VASP under EU law?

Activities that classify as VASP:
- Exchange between virtual and fiat currencies
- Exchange between virtual currencies
- Custody wallet services
- Participation in financial services related to ICOs

BRSPP activities:
- Software infrastructure provision
- Non-custodial protocol
- No exchange services
- No wallet custody

Likely conclusion: NOT a VASP
But: Risk-based approach still recommended
```

### Risk-Based AML Approach

**Even if not classified as VASP, implement:**

**1. Merchant Screening**

Basic due diligence:
```
For all merchants:
- Verify email address
- Check business name legitimacy (if provided)
- Screen against sanctions lists (OFAC, EU)
- Monitor for suspicious activity

For high-volume merchants (>50k EUR/month):
- Request business registration information
- Verify beneficial ownership
- Enhanced monitoring
```

**2. Transaction Monitoring**

Automated monitoring for:
```
Red flags:
- Unusually large transactions (>10k EUR)
- Rapid succession of transactions (potential structuring)
- Transactions with sanctioned addresses
- Patterns consistent with money laundering

Response:
- Flag for manual review
- Request additional information if necessary
- Suspend merchant if severe risk
- Report to authorities if required
```

**3. Record Keeping**

Maintain records:
```
Duration: 5 years minimum

Records:
- Merchant registration information
- Transaction history
- Due diligence documentation
- Suspicious activity reports (if filed)
- Communications with authorities
```

**4. Suspicious Activity Reporting**

If genuine suspicion of money laundering:
```
Report to:
Bulgaria: State Agency for National Security
Lithuania: Financial Crime Investigation Service

Timeline: Promptly (within 24-48 hours)

Information:
- Nature of suspicious activity
- Merchant information
- Transaction details
- Supporting evidence
```

**5. Staff Training**

Even solo operation:
```
Founder should:
- Understand basic AML concepts
- Know red flags
- Understand reporting obligations
- Document training (annual review)
```

### AML Policy Document

**Create written policy covering:**
```markdown
BRSPP AML/CFT Policy

1. Scope
This policy applies to all merchant relationships and 
platform transactions.

2. Risk Assessment
BRSPP operates a non-custodial payment protocol with 
limited money laundering risk due to:
- No custody of funds
- Transparent blockchain transactions
- Merchant whitelist system

However, we implement proportionate controls.

3. Merchant Due Diligence

Tier 1 (<10k EUR/month):
- Email verification
- Basic sanctions screening

Tier 2 (10k-50k EUR/month):
- Business information collection
- Enhanced sanctions screening
- Source of funds inquiry

Tier 3 (>50k EUR/month):
- Full business verification
- Beneficial owner identification
- Ongoing enhanced monitoring

4. Transaction Monitoring

Automated monitoring for:
- Large transactions (>10k EUR)
- Suspicious patterns
- Sanctioned addresses

5. Reporting

Internal: Suspicious activity logged and reviewed
External: Reported to authorities when required

6. Record Keeping

All records maintained for minimum 5 years

7. Policy Review

Reviewed and updated annually or when regulations change
```

### Sanctions Compliance

**Screening Requirements:**

**OFAC (US Office of Foreign Assets Control):**
- Specially Designated Nationals (SDN) list
- Check merchant wallet addresses
- Check customer wallet addresses (if feasible)

**EU Sanctions:**
- EU consolidated sanctions list
- Similar screening process

**Implementation:**
```javascript
// Basic screening approach
async function screenAddress(address) {
  // Check against known sanctioned addresses
  const sanctionedAddresses = await getSanctionedList();
  
  if (sanctionedAddresses.includes(address.toLowerCase())) {
    // Block transaction
    // Log incident
    // Alert compliance officer
    return { allowed: false, reason: "Sanctioned address" };
  }
  
  return { allowed: true };
}
```

**Tools:**
- Chainalysis (commercial blockchain analytics)
- Elliptic (commercial screening service)
- TRM Labs (transaction risk management)
- Free: OFAC SDN list (updated regularly)

**Limitations:**
- Blockchain addresses are pseudonymous
- Not always possible to link address to entity
- Best effort screening

---

## Terms of Service Framework

### Essential Terms

**BRSPP Terms of Service must include:**

**1. Service Description**
```
"BRSPP provides a software platform that enables merchants 
to accept cryptocurrency payments from customers. The 
protocol facilitates payment routing via smart contracts 
on public blockchains."
```

**2. User Eligibility**
```
"To use BRSPP, you must:
- Be at least 18 years of age
- Have legal capacity to enter contracts
- Not be located in a prohibited jurisdiction
- Comply with all applicable laws
- Not engage in prohibited activities"
```

**3. Account Registration**
```
"Merchants must:
- Provide accurate information
- Verify email address
- Connect cryptocurrency wallet
- Complete onboarding process
- Maintain account security"
```

**4. Merchant Obligations**
```
"Merchants are responsible for:
- Compliance with local laws and regulations
- Proper licensing if required in their jurisdiction
- Tax reporting and payment
- Customer service and dispute resolution
- Refunds and chargebacks (where applicable)
- Accurate product/service descriptions"
```

**5. Protocol Fees**
```
"BRSPP charges a 1% fee on all successful payments. 
Fees are collected automatically via smart contracts. 
Additional network fees (gas) are paid by customers."
```

**6. Prohibited Uses**
```
"Merchants may not use BRSPP for:
- Illegal products or services
- Money laundering or terrorist financing
- Fraudulent activities
- Sanctioned entities or individuals
- Adult content (if policy decision)
- Gambling (if policy decision)
- Pyramid schemes or MLM
- Stolen goods or counterfeit products
- Hacking services
- Any activity violating applicable laws"
```

**7. Service Availability**
```
"BRSPP is provided 'as is' without guarantee of 
uninterrupted service. We may:
- Suspend service for maintenance
- Modify features with notice
- Terminate accounts for Terms violations
- Pause protocol in emergencies"
```

**8. Intellectual Property**
```
"BRSPP and its software, including smart contracts, 
APIs, and documentation, are owned by [Company Name]. 
Merchants receive a limited license to use the platform."
```

**9. Limitation of Liability**
```
"To the maximum extent permitted by law:

BRSPP is not liable for:
- Smart contract bugs or vulnerabilities
- Blockchain network issues
- Cryptocurrency price volatility
- Merchant-customer disputes
- Lost funds due to user error
- Third-party service failures

Maximum liability: Fees paid by merchant in 12 months 
preceding claim, up to 10,000 EUR."
```

**10. Indemnification**
```
"Merchants indemnify BRSPP against claims arising from:
- Merchant's use of the protocol
- Merchant's breach of Terms
- Merchant's violation of laws
- Merchant's customer disputes
- Third-party claims related to merchant's business"
```

**11. Dispute Resolution**
```
"Governing Law: [Bulgarian/Lithuanian law depending on phase]
Jurisdiction: Courts of [Sofia/Vilnius]
Alternative: Arbitration (optional)

Process:
1. Informal resolution (contact support)
2. Mediation (30 days)
3. Binding arbitration or litigation"
```

**12. Changes to Terms**
```
"We may update Terms with 30 days notice. Continued use 
constitutes acceptance. Material changes require explicit 
consent."
```

**13. Termination**
```
"Either party may terminate:
- Merchant: At any time, with 30 days notice
- BRSPP: Immediately for Terms violation, or with 30 
  days notice otherwise

Upon termination:
- Merchant access disabled
- Pending payments processed
- Data retained per Privacy Policy
- Outstanding fees remain due"
```

**14. Force Majeure**
```
"BRSPP not liable for failures due to:
- Natural disasters
- War or terrorism
- Government actions
- Blockchain network failures
- Internet outages
- Other events beyond reasonable control"
```

**15. Entire Agreement**
```
"These Terms constitute the entire agreement between 
merchant and BRSPP, superseding all prior agreements."
```

### Terms Updates

**Process for Updates:**

1. Draft updated Terms
2. Legal review
3. Publish updated version on website
4. Email notification to all merchants (30 days advance)
5. In-dashboard notification
6. Require acknowledgment for next login
7. Effective date clearly stated

**Version Control:**
```
Terms of Service v1.0 - Effective January 1, 2026
Terms of Service v1.1 - Effective April 1, 2026
```

Keep archived versions accessible.

---

## Merchant Agreements

### Merchant Onboarding Agreement

**Separate from Terms of Service:**

**Purpose:** Formalize merchant relationship

**Key Sections:**

**1. Services Provided**
```
"BRSPP provides:
- Access to payment protocol
- API endpoints
- Merchant dashboard
- Webhook notifications
- Technical documentation
- Customer support"
```

**2. Merchant Representations**
```
"Merchant represents and warrants:
- Legal authority to enter agreement
- Business is legitimate and lawful
- Compliance with applicable regulations
- Accurate information provided
- Proper licensing obtained if required"
```

**3. Payment Terms**
```
"Protocol Fee: 1% of transaction value
Collection: Automatic via smart contract
Invoice: Monthly statement provided
Disputes: 30 days to raise fee disputes"
```

**4. Confidentiality**
```
"Both parties agree to:
- Protect confidential information
- Not disclose to third parties
- Use only for agreement purposes
- Return or destroy upon termination"
```

**5. Data Processing**
```
"BRSPP processes merchant and customer data as 
described in Privacy Policy. Merchant appoints 
BRSPP as data processor. Data Processing Agreement 
attached as Exhibit A."
```

**6. Term and Termination**
```
"Initial Term: 12 months
Renewal: Automatic unless terminated
Termination: 30 days written notice
Effect: Access discontinued, data handled per policy"
```

### Data Processing Agreement

**Required under GDPR:**

**Key Elements:**
```markdown
Data Processing Agreement

Between: [Merchant Name] (Controller)
And: BRSPP (Processor)

1. Subject Matter
Processing of personal data in connection with payment 
processing services

2. Duration
Duration of merchant agreement

3. Nature and Purpose
To facilitate cryptocurrency payments between merchant's 
customers and merchant

4. Types of Personal Data
- Customer wallet addresses
- Transaction amounts and timestamps
- Optional: customer email addresses

5. Categories of Data Subjects
- Merchant's customers
- End users of merchant's services

6. Processor Obligations
BRSPP shall:
- Process data only on documented instructions
- Ensure confidentiality of processing personnel
- Implement appropriate security measures
- Assist with data subject rights requests
- Assist with compliance obligations
- Delete or return data upon termination
- Make available information for audits

7. Sub-Processors
Current sub-processors:
- Cloud hosting provider: [AWS/Google Cloud]
- Analytics: [Google Analytics]

Merchant consents to use of sub-processors listed above.

8. Security Measures
- Encryption at rest and in transit
- Access controls
- Regular security assessments
- Incident response procedures

9. Data Breach Notification
BRSPP will notify merchant within 24 hours of becoming 
aware of any data breach

10. Audit Rights
Merchant may audit BRSPP's compliance once per year with 
reasonable notice
```

---

## Compliance Monitoring

### Ongoing Compliance Activities

**Monthly:**
- Review new merchant registrations
- Check for suspicious transaction patterns
- Update sanctions screening lists
- Review customer support tickets for compliance issues

**Quarterly:**
- Review Terms of Service for needed updates
- Assess regulatory developments (MiCA, PSD2, etc.)
- Audit data processing activities
- Review security incidents

**Annually:**
- Full compliance audit
- Update AML policy
- Review and update Privacy Policy
- Legal review of all agreements
- Staff/founder AML training
- Review insurance coverage
- Assess need for licenses in new jurisdictions

### Compliance Checklist

**Pre-Launch Checklist:**
```
Legal Structure:
☐ Company incorporated
☐ Terms of Service finalized
☐ Privacy Policy published
☐ Merchant Agreement template ready
☐ Data Processing Agreement template ready

Regulatory:
☐ Legal opinion obtained on classification
☐ AML policy documented
☐ Sanctions screening implemented
☐ Record keeping system in place

Technical:
☐ GDPR compliance verified (data minimization, etc.)
☐ Security measures implemented
☐ Data encryption enabled
☐ Backup and recovery procedures tested

Operational:
☐ Customer support channels established
☐ Compliance monitoring procedures defined
☐ Incident response plan documented
☐ Insurance coverage obtained (if applicable)
```

**Post-Launch Ongoing:**
```
Weekly:
☐ Monitor for unusual activity
☐ Review high-value transactions
☐ Check system logs for errors

Monthly:
☐ Generate compliance reports
☐ Review merchant statistics
☐ Update sanctions lists

Quarterly:
☐ Regulatory environment scan
☐ Compliance metrics review
☐ Policy updates (if needed)

Annually:
☐ Full compliance audit
☐ Legal review
☐ Insurance renewal
☐ Training completion
```

### Regulatory Change Management

**Process:**

1. **Monitoring:**
   - Subscribe to regulatory updates
   - Follow industry associations
   - Engage with crypto lawyers
   - Monitor competitor compliance

2. **Assessment:**
   - Evaluate impact on BRSPP
   - Determine compliance requirements
   - Estimate costs and timeline
   - Risk assessment

3. **Planning:**
   - Develop compliance roadmap
   - Allocate resources
   - Set deadlines
   - Identify dependencies

4. **Implementation:**
   - Update policies and procedures
   - Modify technical systems if needed
   - Train team members
   - Update documentation

5. **Verification:**
   - Internal compliance audit
   - External legal review (if major change)
   - Document compliance
   - Ongoing monitoring

### Compliance Resources

**Internal:**
- Compliance documentation folder
- Policy templates
- Checklist templates
- Incident response procedures

**External:**
- Legal counsel (crypto specialist)
- Accountant/tax advisor
- Compliance consultant (if needed)
- Industry associations (blockchain industry groups)

**Regulatory Bodies:**

**Bulgaria:**
- Bulgarian National Bank (banking supervision)
- Commission for Personal Data Protection (GDPR)
- National Revenue Agency (tax compliance)

**Lithuania:**
- Bank of Lithuania (financial supervision)
- Financial Crime Investigation Service (AML)
- State Data Protection Inspectorate (GDPR)

---

## Related Documentation

**Legal:**
- [Corporate Structure](./STRUCTURE.md) - Business entity strategy

**Architecture:**
- [Security Mechanisms](../architecture/SECURITY.md) - Technical security features

**Development:**
- [Development Timeline](../development/TIMELINE.md) - Implementation roadmap

---

[Back to Documentation Index](../README.md) | [Main Repository](../../README.md)

---

**Last Updated:** November 2025  
**Version:** 1.0.0
