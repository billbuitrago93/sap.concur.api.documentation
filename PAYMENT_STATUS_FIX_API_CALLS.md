# Payment Status Fix - API Calls with FIS Document IDs

## Reports to Fix

| Report ID | FIS Document ID | Status |
|-----------|----------------|--------|
| 31F470F6964F478DA609 | 9d26e15fe0ac4621883ff5933bba84b1 | P_FAIL |
| FC904845DB4140FAA37F | 1f7b13d3115b45d482cbfa47ce50bf37 | P_FAIL |
| 87C24BC17A34471E86A1 | 9596fe7748524ef498d0dc7f6c425086 | P_FAIL |
| 50764E4FE29244108B77 | 0a1abb4edc4d4d0eba807eaeb42515b1 | P_FAIL |
| 8760F7C96ECC446481E4 | TBD (retrieve from logs) | P_FAIL |

---

## IMPORTANT: Prerequisites

Before executing these API calls, you need to gather the following information from Sage Intacct for each report:

### Required Sage Intacct Details:
For each expense report, you'll need from the **successful first posting**:
- Company/Entity ID
- Document Number (created in Sage Intacct)
- Fiscal Year (e.g., "2025")
- Company Code
- Payment Reference
- Employee ID
- Employee Name
- Total Amount
- Currency Code

You can get the SAP Concur details (amounts, employee info) using the v3 Reports API (see examples below).

---

## Step-by-Step Process for Each Report

For each report, you need to:
1. **Get report details** (to gather employee info and amounts)
2. **Send posting confirmation** (changes P_FAIL → "Sent for Payment")
3. **Send payment confirmation** (changes "Sent for Payment" → "Payment Confirmed")

---

## Report 1: 31F470F6964F478DA609

### Step 1.1: Get Report Details

```bash
curl --location 'https://us2.api.concursolutions.com/api/v3.0/expense/reports/31F470F6964F478DA609?user={OWNER_EMAIL}' \
--header 'Accept: application/json' \
--header 'Authorization: Bearer {YOUR_TOKEN}'
```

### Step 1.2: Send Posting Confirmation

**USE THE FIS DOCUMENT ID**: `9d26e15fe0ac4621883ff5933bba84b1`

```bash
curl --location 'https://us2.api.concursolutions.com/financialintegration/fi/v4/companies/transactiontypes/expense/transactions/postingconfirmations' \
--header 'Content-Type: application/json' \
--header 'Authorization: Bearer {YOUR_TOKEN}' \
--data '{
    "systemId": "",
    "postingConfirmations": [
        {
            "docId": "9d26e15fe0ac4621883ff5933bba84b1",
            "overallPostingStatusCode": "success",
            "postingDocs": [
                {
                    "companyId": "{SAGE_COMPANY_ID}",
                    "documentNumber": "{SAGE_DOC_NUMBER}",
                    "fiscalYear": "2025",
                    "paymentRelevantLineItems": [],
                    "postingDate": "2025-11-21"
                }
            ],
            "systemMessages": [
                {
                    "messageId": "SUCCESS",
                    "messageLanguage": "EN",
                    "messageShortText": "Successfully posted to Sage Intacct"
                }
            ]
        }
    ]
}'
```

### Step 1.3: Send Payment Confirmation

```bash
curl --location 'https://us2.api.concursolutions.com/financialintegration/fi/v4/companies/transactiontypes/expense/transactions/paymentconfirmations' \
--header 'Content-Type: application/json' \
--header 'Authorization: Bearer {YOUR_TOKEN}' \
--data '{
    "systemId": "",
    "processingConfirmation": [
        {
            "docId": "9d26e15fe0ac4621883ff5933bba84b1",
            "processingStatusCode": "CP",
            "clearingDetails": [
                {
                    "clearingDate": "2025-11-21T12:00:00.000Z",
                    "clearingAmount": {AMOUNT},
                    "clearingCurrency": "{CURRENCY}",
                    "receiver": {
                        "receiverId": "{EMPLOYEE_ID}",
                        "receiverName": "{EMPLOYEE_NAME}",
                        "receiverType": "EMPLOYEE"
                    },
                    "clearingReference": {
                        "companyCode": "{COMPANY_CODE}",
                        "financialDocumentId": "{SAGE_DOC_ID}",
                        "fiscalYear": "2025",
                        "paymentRef": "{PAYMENT_REFERENCE}",
                        "paymentMethod": "E"
                    }
                }
            ]
        }
    ]
}'
```

---

## Report 2: FC904845DB4140FAA37F

### Step 2.1: Get Report Details

```bash
curl --location 'https://us2.api.concursolutions.com/api/v3.0/expense/reports/FC904845DB4140FAA37F?user={OWNER_EMAIL}' \
--header 'Accept: application/json' \
--header 'Authorization: Bearer {YOUR_TOKEN}'
```

### Step 2.2: Send Posting Confirmation

**USE THE FIS DOCUMENT ID**: `1f7b13d3115b45d482cbfa47ce50bf37`

```bash
curl --location 'https://us2.api.concursolutions.com/financialintegration/fi/v4/companies/transactiontypes/expense/transactions/postingconfirmations' \
--header 'Content-Type: application/json' \
--header 'Authorization: Bearer {YOUR_TOKEN}' \
--data '{
    "systemId": "",
    "postingConfirmations": [
        {
            "docId": "1f7b13d3115b45d482cbfa47ce50bf37",
            "overallPostingStatusCode": "success",
            "postingDocs": [
                {
                    "companyId": "{SAGE_COMPANY_ID}",
                    "documentNumber": "{SAGE_DOC_NUMBER}",
                    "fiscalYear": "2025",
                    "paymentRelevantLineItems": [],
                    "postingDate": "2025-11-21"
                }
            ],
            "systemMessages": [
                {
                    "messageId": "SUCCESS",
                    "messageLanguage": "EN",
                    "messageShortText": "Successfully posted to Sage Intacct"
                }
            ]
        }
    ]
}'
```

### Step 2.3: Send Payment Confirmation

```bash
curl --location 'https://us2.api.concursolutions.com/financialintegration/fi/v4/companies/transactiontypes/expense/transactions/paymentconfirmations' \
--header 'Content-Type: application/json' \
--header 'Authorization: Bearer {YOUR_TOKEN}' \
--data '{
    "systemId": "",
    "processingConfirmation": [
        {
            "docId": "1f7b13d3115b45d482cbfa47ce50bf37",
            "processingStatusCode": "CP",
            "clearingDetails": [
                {
                    "clearingDate": "2025-11-21T12:00:00.000Z",
                    "clearingAmount": {AMOUNT},
                    "clearingCurrency": "{CURRENCY}",
                    "receiver": {
                        "receiverId": "{EMPLOYEE_ID}",
                        "receiverName": "{EMPLOYEE_NAME}",
                        "receiverType": "EMPLOYEE"
                    },
                    "clearingReference": {
                        "companyCode": "{COMPANY_CODE}",
                        "financialDocumentId": "{SAGE_DOC_ID}",
                        "fiscalYear": "2025",
                        "paymentRef": "{PAYMENT_REFERENCE}",
                        "paymentMethod": "E"
                    }
                }
            ]
        }
    ]
}'
```

---

## Report 3: 87C24BC17A34471E86A1

### Step 3.1: Get Report Details

```bash
curl --location 'https://us2.api.concursolutions.com/api/v3.0/expense/reports/87C24BC17A34471E86A1?user={OWNER_EMAIL}' \
--header 'Accept: application/json' \
--header 'Authorization: Bearer {YOUR_TOKEN}'
```

### Step 3.2: Send Posting Confirmation

**USE THE FIS DOCUMENT ID**: `9596fe7748524ef498d0dc7f6c425086`

```bash
curl --location 'https://us2.api.concursolutions.com/financialintegration/fi/v4/companies/transactiontypes/expense/transactions/postingconfirmations' \
--header 'Content-Type: application/json' \
--header 'Authorization: Bearer {YOUR_TOKEN}' \
--data '{
    "systemId": "",
    "postingConfirmations": [
        {
            "docId": "9596fe7748524ef498d0dc7f6c425086",
            "overallPostingStatusCode": "success",
            "postingDocs": [
                {
                    "companyId": "{SAGE_COMPANY_ID}",
                    "documentNumber": "{SAGE_DOC_NUMBER}",
                    "fiscalYear": "2025",
                    "paymentRelevantLineItems": [],
                    "postingDate": "2025-11-21"
                }
            ],
            "systemMessages": [
                {
                    "messageId": "SUCCESS",
                    "messageLanguage": "EN",
                    "messageShortText": "Successfully posted to Sage Intacct"
                }
            ]
        }
    ]
}'
```

### Step 3.3: Send Payment Confirmation

```bash
curl --location 'https://us2.api.concursolutions.com/financialintegration/fi/v4/companies/transactiontypes/expense/transactions/paymentconfirmations' \
--header 'Content-Type: application/json' \
--header 'Authorization: Bearer {YOUR_TOKEN}' \
--data '{
    "systemId": "",
    "processingConfirmation": [
        {
            "docId": "9596fe7748524ef498d0dc7f6c425086",
            "processingStatusCode": "CP",
            "clearingDetails": [
                {
                    "clearingDate": "2025-11-21T12:00:00.000Z",
                    "clearingAmount": {AMOUNT},
                    "clearingCurrency": "{CURRENCY}",
                    "receiver": {
                        "receiverId": "{EMPLOYEE_ID}",
                        "receiverName": "{EMPLOYEE_NAME}",
                        "receiverType": "EMPLOYEE"
                    },
                    "clearingReference": {
                        "companyCode": "{COMPANY_CODE}",
                        "financialDocumentId": "{SAGE_DOC_ID}",
                        "fiscalYear": "2025",
                        "paymentRef": "{PAYMENT_REFERENCE}",
                        "paymentMethod": "E"
                    }
                }
            ]
        }
    ]
}'
```

---

## Report 4: 50764E4FE29244108B77

### Step 4.1: Get Report Details

```bash
curl --location 'https://us2.api.concursolutions.com/api/v3.0/expense/reports/50764E4FE29244108B77?user={OWNER_EMAIL}' \
--header 'Accept: application/json' \
--header 'Authorization: Bearer {YOUR_TOKEN}'
```

### Step 4.2: Send Posting Confirmation

**USE THE FIS DOCUMENT ID**: `0a1abb4edc4d4d0eba807eaeb42515b1`

```bash
curl --location 'https://us2.api.concursolutions.com/financialintegration/fi/v4/companies/transactiontypes/expense/transactions/postingconfirmations' \
--header 'Content-Type: application/json' \
--header 'Authorization: Bearer {YOUR_TOKEN}' \
--data '{
    "systemId": "",
    "postingConfirmations": [
        {
            "docId": "0a1abb4edc4d4d0eba807eaeb42515b1",
            "overallPostingStatusCode": "success",
            "postingDocs": [
                {
                    "companyId": "{SAGE_COMPANY_ID}",
                    "documentNumber": "{SAGE_DOC_NUMBER}",
                    "fiscalYear": "2025",
                    "paymentRelevantLineItems": [],
                    "postingDate": "2025-11-21"
                }
            ],
            "systemMessages": [
                {
                    "messageId": "SUCCESS",
                    "messageLanguage": "EN",
                    "messageShortText": "Successfully posted to Sage Intacct"
                }
            ]
        }
    ]
}'
```

### Step 4.3: Send Payment Confirmation

```bash
curl --location 'https://us2.api.concursolutions.com/financialintegration/fi/v4/companies/transactiontypes/expense/transactions/paymentconfirmations' \
--header 'Content-Type: application/json' \
--header 'Authorization: Bearer {YOUR_TOKEN}' \
--data '{
    "systemId": "",
    "processingConfirmation": [
        {
            "docId": "0a1abb4edc4d4d0eba807eaeb42515b1",
            "processingStatusCode": "CP",
            "clearingDetails": [
                {
                    "clearingDate": "2025-11-21T12:00:00.000Z",
                    "clearingAmount": {AMOUNT},
                    "clearingCurrency": "{CURRENCY}",
                    "receiver": {
                        "receiverId": "{EMPLOYEE_ID}",
                        "receiverName": "{EMPLOYEE_NAME}",
                        "receiverType": "EMPLOYEE"
                    },
                    "clearingReference": {
                        "companyCode": "{COMPANY_CODE}",
                        "financialDocumentId": "{SAGE_DOC_ID}",
                        "fiscalYear": "2025",
                        "paymentRef": "{PAYMENT_REFERENCE}",
                        "paymentMethod": "E"
                    }
                }
            ]
        }
    ]
}'
```

---

## Report 5: 8760F7C96ECC446481E4

**Note:** FIS Document ID still needed. Check Azure logs using the same query you used for the other 4 reports.

Once you have the FIS Document ID for this report, follow the same 3-step process above.

---

## Placeholders to Replace

Before executing the API calls, replace these placeholders with actual values:

### From SAP Concur (get via API in Step X.1):
- `{OWNER_EMAIL}` - Employee's email address
- `{EMPLOYEE_ID}` - Employee ID
- `{EMPLOYEE_NAME}` - Employee full name
- `{AMOUNT}` - Total amount (e.g., 32195.00)
- `{CURRENCY}` - Currency code (e.g., "INR", "USD")

### From Sage Intacct (from successful first posting):
- `{SAGE_COMPANY_ID}` - Your Sage Intacct company/entity ID
- `{SAGE_DOC_NUMBER}` - Document number created in Sage Intacct
- `{COMPANY_CODE}` - Company code in Sage Intacct
- `{SAGE_DOC_ID}` - Sage Intacct document ID
- `{PAYMENT_REFERENCE}` - Payment reference (can use format: `{COMPANY_CODE}/{DOC_ID}/{FISCAL_YEAR}/1`)

### Authentication:
- `{YOUR_TOKEN}` - Your company-level access token with `FISVC` scope

---

## Execution Checklist

For each report:
- [ ] Get report details from SAP Concur (Step X.1)
- [ ] Get Sage Intacct document details from your ERP
- [ ] Replace all placeholders in the API calls
- [ ] Execute posting confirmation (Step X.2)
- [ ] Verify posting confirmation succeeded (HTTP 200)
- [ ] Execute payment confirmation (Step X.3)
- [ ] Verify payment confirmation succeeded (HTTP 200)
- [ ] Verify final payment status changed to P_PAYC

---

## Expected Results

After executing both API calls for each report:

### Posting Confirmation Response:
```json
[
    {
        "code": 200,
        "docId": "9d26e15fe0ac4621883ff5933bba84b1",
        "systemId": "",
        "postingConfirmationResult": "SUCCESS",
        "errorMessage": "",
        "detailMessage": ""
    }
]
```

### Payment Confirmation Response:
```json
[
    {
        "code": 200,
        "docId": "9d26e15fe0ac4621883ff5933bba84b1",
        "systemId": "",
        "paymentConfirmationResult": "SUCCESS",
        "errorMessage": "",
        "detailMessage": ""
    }
]
```

### Final Report Status:
- `PaymentStatusCode`: `P_PAYC` (Payment Confirmed)
- `PaymentStatusName`: "Payment Confirmed"
- `PaidDate`: Will be populated with the payment date

---

## Troubleshooting

### Error: "This document does not exist"
**Cause:** Using the wrong document ID (report ID instead of FIS document ID)
**Solution:** Ensure you're using the 32-character FIS document ID from Azure logs

### Error: "Invalid posting status code"
**Cause:** Using wrong status code
**Solution:** Use `"success"` for posting confirmation, `"CP"` for payment confirmation

### Error: Authentication failure
**Cause:** Token doesn't have FISVC scope
**Solution:** Ensure your access token has the `FISVC` scope

### Status doesn't change after posting confirmation
**Note:** This is expected. Status only changes to P_PAYC after the payment confirmation (Step X.3)

---

## Quick Reference: Processing Status Codes

For payment confirmation `processingStatusCode`:
- `CP` = Completely Paid ← Use this
- `PP` = Partially Paid
- `RE` = Reversal
- `OB` = Obsolete

---

## Next Steps

1. Retrieve the FIS Document ID for Report 5 (8760F7C96ECC446481E4) from Azure logs
2. Gather Sage Intacct details for all 5 reports
3. Gather SAP Concur employee details using the Step X.1 API calls
4. Replace all placeholders with actual values
5. Execute posting confirmations for all 5 reports
6. Execute payment confirmations for all 5 reports
7. Verify all reports show P_PAYC status

Good luck!
