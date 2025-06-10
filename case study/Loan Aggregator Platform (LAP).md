A global digital Aggregator Platform wants allow customers to apply for personal loans online or via mobile. The system integrates with external services like banks, credit bureaus, fraud detection systems, and document verification platforms.

#### Loan Application Submitted
* Customer submits loan application via aggregator’s web/mobile app.
* System emits event: LoanApplied.
#### Credit Score Check (centralized)
* It calls the Credit Bureau .
* Emits event: CreditChecked.
#### Risk Assessment per Bank (Parallelized per Bank)
* Depends on Credit Score 
* Each bank applies its own risk algorithms to compute loan eligibility and terms (interest rate, max loan amount).
* Emits event: RiskAssessed[BankID] .
#### KYC Verification (centralized)
* Triggers third-party KYC for customer verification 
* Emits event: KycVerified.
#### Loan Approval (Per Bank Offer)
* Approves or rejects the loan based on risk & KYC results.
* Emits event: LoanApproved[BankID] or LoanRejected[BankID].
#### Aggregation and Selection
* Compiles list of approved loan offers from multiple banks with terms.
* Sends list of offers back to customer for selection.
#### Customer Selects Loan Offer
* Customer picks preferred loan offer from the list.
* Emits event: LoanSelected[BankID].
#### Loan Disbursement
* Reserves funds with chosen bank.
* Calls payment gateway or bank API for fund disbursement.
* Emits event: LoanDisbursed[BankID].
#### Notification
* Sends confirmation email/SMS to customer with loan details and payment schedule.



## Challenges
- Frequent timeouts from third-party APIs (e.g., Credit Score API).
- The KYC Verification Service from a third-party provider sometimes goes down for several minutes.
- Prevent abuse or exceeding third-party quotas.
- Repeated KYC lookups for the same customer (customer submits many application or make modifications)
- Interacting with external unreliable systems (banks, bureaus, KYC vendors). Many of these interactions may fail or time out. 
- 
