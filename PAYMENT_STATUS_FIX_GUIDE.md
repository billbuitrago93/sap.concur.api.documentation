# Guide: Fix Payment Status from "Financial Posting Failed" to "Paid"

## Overview
This guide provides the complete process to update payment status for expense reports stuck in "Financial Posting Failed" status due to duplicate processing in the FIS integration.

## Reports to Fix

| Report ID | Owner Email |
|-----------|-------------|
| 31F470F6964F478DA609 | TBD |
| FC904845DB4140FAA37F | TBD |
| 87C24BC17A34471E86A1 | TBD |
| 50764E4FE29244108B77 | TBD |
| 8760F7C96ECC446481E4 | mahesh.kumar@axi.com |

## Step 1: Verify Each Report Status

For each report, retrieve its details to confirm the payment status and gather necessary information.

### API Call Template

```bash
curl --location 'https://us2.api.concursolutions.com/api/v3.0/expense/reports/{REPORT_ID}?user={OWNER_EMAIL}' \
--header 'Accept: application/json' \
--header 'Authorization: Bearer {YOUR_TOKEN}'
```

### Example for Report: 8760F7C96ECC446481E4

```bash
curl --location 'https://us2.api.concursolutions.com/api/v3.0/expense/reports/8760F7C96ECC446481E4?user=mahesh.kumar@axi.com' \
--header 'Accept: application/json' \
--header 'Authorization: Bearer {YOUR_TOKEN}'
```

**Expected Response Fields:**
- `PaymentStatusCode`: `P_FAIL` (Financial Posting Failed)
- `PaymentStatusName`: "Financial Posting Failed"
- `ApprovalStatusCode`: `A_APPR` (Approved)
- `Total`: Total amount in the specified currency
- `CurrencyCode`: Currency code (e.g., "INR", "USD")
- `OwnerLoginID`: Employee email
- `OwnerName`: Employee name

## Step 2: Send Posting Confirmation (Success)

This changes the payment status from "Financial Posting Failed" → "Sent for Payment"

### API Call Template

```bash
curl --location 'https://us2.api.concursolutions.com/financialintegration/fi/v4/companies/transactiontypes/expense/transactions/postingconfirmations' \
--header 'Authorization: Bearer {YOUR_TOKEN}' \
--header 'Content-Type: application/json' \
--data '{
  "systemId": "",
  "postingConfirmations": [
    {
      "docId": "{REPORT_ID}",
      "overallPostingStatusCode": "success",
      "postingDocs": [
        {
          "companyId": "{SAGE_COMPANY_ID}",
          "documentNumber": "{SAGE_DOCUMENT_NUMBER}",
          "fiscalYear": "{FISCAL_YEAR}",
          "paymentRelevantLineItems": [],
          "postingDate": "{POSTING_DATE}"
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

### Parameters to Replace:

- `{YOUR_TOKEN}`: Your company-level access token with `FISVC` scope
- `{REPORT_ID}`: The expense report ID (e.g., `8760F7C96ECC446481E4`)
- `{SAGE_COMPANY_ID}`: Your Sage Intacct company/entity ID
- `{SAGE_DOCUMENT_NUMBER}`: The document number created in Sage Intacct on first (successful) attempt
- `{FISCAL_YEAR}`: Fiscal year (e.g., `2025`)
- `{POSTING_DATE}`: Date in format `yyyy-MM-dd` (e.g., `2025-11-21`)

### Example for Report: 8760F7C96ECC446481E4

```bash
curl --location 'https://us2.api.concursolutions.com/financialintegration/fi/v4/companies/transactiontypes/expense/transactions/postingconfirmations' \
--header 'Authorization: Bearer {YOUR_TOKEN}' \
--header 'Content-Type: application/json' \
--data '{
  "systemId": "",
  "postingConfirmations": [
    {
      "docId": "8760F7C96ECC446481E4",
      "overallPostingStatusCode": "success",
      "postingDocs": [
        {
          "companyId": "YOUR_COMPANY_ID",
          "documentNumber": "YOUR_SAGE_DOC_NUMBER",
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

**Expected Response:**
- HTTP 200 OK
- No error messages

## Step 3: Send Payment Confirmation (Paid)

This changes the payment status from "Sent for Payment" → "Payment Confirmed" (Paid)

### API Call Template

```bash
curl --location 'https://us2.api.concursolutions.com/financialintegration/fi/v4/companies/transactiontypes/expense/transactions/paymentconfirmations' \
--header 'Authorization: Bearer {YOUR_TOKEN}' \
--header 'Content-Type: application/json' \
--data '{
  "systemId": "",
  "processingConfirmation": [
    {
      "docId": "{REPORT_ID}",
      "processingStatusCode": "CP",
      "clearingDetails": [
        {
          "clearingDate": "{CLEARING_DATE}",
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
            "fiscalYear": "{FISCAL_YEAR}",
            "paymentRef": "{PAYMENT_REFERENCE}",
            "paymentMethod": "E"
          }
        }
      ]
    }
  ]
}'
```

### Parameters to Replace:

- `{YOUR_TOKEN}`: Your company-level access token with `FISVC` scope
- `{REPORT_ID}`: The expense report ID
- `{CLEARING_DATE}`: Date/time in ISO format `yyyy-MM-ddTHH:mm:ss.SSSZ` (e.g., `2025-11-21T12:00:00.000Z`)
- `{AMOUNT}`: Total amount (e.g., `32195.00`)
- `{CURRENCY}`: Currency code (e.g., `INR`, `USD`)
- `{EMPLOYEE_ID}`: Employee ID from your system
- `{EMPLOYEE_NAME}`: Employee full name
- `{COMPANY_CODE}`: Your company code in Sage Intacct
- `{SAGE_DOC_ID}`: The Sage Intacct document ID
- `{FISCAL_YEAR}`: Fiscal year (e.g., `2025`)
- `{PAYMENT_REFERENCE}`: Payment reference (can be formatted as `{COMPANY_CODE}/{DOC_ID}/{FISCAL_YEAR}/1`)
- **Processing Status Codes:**
  - `CP` = Completely Paid ← Use this
  - `PP` = Partially Paid
  - `RE` = Reversal
  - `OB` = Obsolete

### Example for Report: 8760F7C96ECC446481E4

```bash
curl --location 'https://us2.api.concursolutions.com/financialintegration/fi/v4/companies/transactiontypes/expense/transactions/paymentconfirmations' \
--header 'Authorization: Bearer {YOUR_TOKEN}' \
--header 'Content-Type: application/json' \
--data '{
  "systemId": "",
  "processingConfirmation": [
    {
      "docId": "8760F7C96ECC446481E4",
      "processingStatusCode": "CP",
      "clearingDetails": [
        {
          "clearingDate": "2025-11-21T12:00:00.000Z",
          "clearingAmount": 32195.00,
          "clearingCurrency": "INR",
          "receiver": {
            "receiverId": "YOUR_EMPLOYEE_ID",
            "receiverName": "Kummari Uma Mahesh Kumar",
            "receiverType": "EMPLOYEE"
          },
          "clearingReference": {
            "companyCode": "YOUR_COMPANY_CODE",
            "financialDocumentId": "YOUR_SAGE_DOC_ID",
            "fiscalYear": "2025",
            "paymentRef": "YOUR_PAYMENT_REF",
            "paymentMethod": "E"
          }
        }
      ]
    }
  ]
}'
```

**Expected Response:**
- HTTP 200 OK
- No error messages

## Step 4: Verify the Fix

After sending both confirmations, verify the payment status has been updated:

```bash
curl --location 'https://us2.api.concursolutions.com/api/v3.0/expense/reports/{REPORT_ID}?user={OWNER_EMAIL}' \
--header 'Accept: application/json' \
--header 'Authorization: Bearer {YOUR_TOKEN}'
```

**Expected Result:**
- `PaymentStatusCode`: Should change from `P_FAIL` to `P_PAYC` (Payment Confirmed)
- `PaymentStatusName`: Should change from "Financial Posting Failed" to "Payment Confirmed" or "Paid"
- `PaidDate`: Should be populated with the payment confirmation date

## Required Information Checklist

To complete this process for all 5 reports, you'll need:

### From SAP Concur (via API):
- [x] Report IDs
- [ ] Owner email addresses for each report
- [ ] Total amounts
- [ ] Currency codes
- [ ] Employee names
- [ ] Employee IDs

### From Sage Intacct (from the successful first posting):
- [ ] Company/Entity ID
- [ ] Document numbers (from successful postings)
- [ ] Company codes
- [ ] Fiscal year
- [ ] Payment references

## Troubleshooting

### Error: "A resource with the specified ID could not be found"
**Solution:** Include the `user` parameter with the report owner's email address.

### Error: "Invalid payment status code"
**Note:** The custom payment status code for "Financial Posting Failed" is `P_FAIL`, not `P_POSTF`.

### Error: Token doesn't have required scope
**Solution:** Ensure your access token has the `FISVC` scope for Financial Integration Service operations.

### Empty Response from FIS GET Transactions
**Note:** This is expected. Once expenses are processed (success or failure), they're removed from the FIS queue. You can still send confirmations using the report ID.

## Authentication Requirements

- **Scope Required:** `FISVC` (Financial Integration Service)
- **Token Type:** Company-level access token (not user-level)
- **Base URL:** `https://us2.api.concursolutions.com` (adjust for your data center)

## Summary of Process

1. **Verify** each report's current status and gather details (Step 1)
2. **Send posting confirmation** with "success" status to move from "Financial Posting Failed" → "Sent for Payment" (Step 2)
3. **Send payment confirmation** with "CP" status to move from "Sent for Payment" → "Payment Confirmed/Paid" (Step 3)
4. **Verify** the fix was successful (Step 4)

## Notes

- The report IDs you have are **20 characters** long, which is valid despite documentation mentioning 32 characters
- You must process Step 2 before Step 3 - they cannot be done in reverse order
- The FIS API accepts confirmations even if the document is no longer in the processing queue
- Since the expense was already created successfully in Sage Intacct on the first attempt, use those existing document details in your confirmations

## Next Steps

1. Gather the Sage Intacct document details for each of the 5 reports
2. Fill in the templates above with the correct values
3. Execute Step 2 (posting confirmation) for each report
4. Execute Step 3 (payment confirmation) for each report
5. Verify all 5 reports now show "Payment Confirmed" status
