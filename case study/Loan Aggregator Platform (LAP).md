A global digital Aggregator Platform wants allow customers to apply for personal loans online or via mobile. The system integrates with external services like banks, credit bureaus, fraud detection systems, and document verification platforms.

steps
### Loan Application Submitted
- Emit event: LoanApplied
### Credit Score Check (Service A) 
- Calls external Credit Bureau
- Emit CreditChecked
### Risk Assessment (Service B) → Listens to CreditChecked → Computes risk profile → Emit RiskAssessed
KYC Verification (Service C) → Listens to RiskAssessed → Triggers third-party KYC → Emit KycVerified
Loan Approval (Service D) → Listens to KycVerified → Approves loan → Emit LoanApproved
Loan Disbursement (Service E) → Listens to LoanApproved → Reserves funds → Calls payment gateway → Emit LoanDisbursed
Notification (Service F) → Listens to LoanDisbursed → Sends email/SMS confirmation
