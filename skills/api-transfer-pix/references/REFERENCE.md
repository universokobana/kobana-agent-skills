# Kobana Transfer API - Complete Reference

Complete API documentation for managing Pix transfers, batches, and financial accounts.

## Base URLs

| Environment | URL |
|-------------|-----|
| Production | `https://api.kobana.com.br` |
| Sandbox | `https://api-sandbox.kobana.com.br` |

## Authentication

```
Authorization: Bearer {your_api_token}
```

## Common Headers

| Header | Required | Description |
|--------|----------|-------------|
| `Authorization` | Yes | Bearer token |
| `Content-Type` | Yes | `application/json` |
| `User-Agent` | No | Valid email for contact |
| `X-Idempotency-Key` | No | Prevents replay processing |

---

## Pix Transfers

### List Pix Transfers
```
GET /v2/transfer/pix
```

**Query Parameters:**
| Parameter | Type | Description |
|-----------|------|-------------|
| `page` | integer | Page number |
| `per_page` | integer | Items per page (default 50, max 50) |
| `status` | string | Filter by status |
| `registration_status` | string | Filter by registration status |
| `financial_account_uid` | string | Filter by financial account |

### Create Pix Transfer
```
POST /v2/transfer/pix
```

**Required Parameters:**
| Parameter | Type | Description |
|-----------|------|-------------|
| `amount` | decimal | Transfer amount in BRL |
| `financial_account_uid` | string | UUID of the source financial account |
| `type` | string | `key` or `bank_account` |

**Required for `type: "key"`:**
| Parameter | Type | Description |
|-----------|------|-------------|
| `key_type` | string | `cpf`, `cnpj`, `email`, `phone`, `random` |
| `key` | string | The Pix key value |

**Required for `type: "bank_account"`:**
| Parameter | Type | Description |
|-----------|------|-------------|
| `bank_account.compe_number` | integer | Bank COMPE code (e.g., 341 for Itaú) |
| `bank_account.agency_number` | string | Agency number |
| `bank_account.account_number` | string | Account number |
| `bank_account.account_digit` | string | Account check digit |
| `bank_account.document_number` | string | Beneficiary CPF/CNPJ |

**Optional Parameters:**
| Parameter | Type | Description |
|-----------|------|-------------|
| `scheduled_to` | date | Schedule date (YYYY-MM-DD) |
| `transfer_purpose` | string | Purpose code (default: "98") |
| `beneficiary.document_number` | string | Beneficiary CPF/CNPJ |
| `beneficiary.name` | string | Beneficiary name |
| `bank_account.ispb_number` | integer | Bank ISPB code |
| `bank_account.agency_digit` | string | Agency check digit |
| `identifier` | string | Payment identifier (bank-specific) |
| `external_id` | string | External ID for tracking |
| `custom_data` | object | Custom metadata (JSON) |
| `tags` | array | Tags for organization |

**Example Request (by Pix Key):**
```json
{
  "amount": 100.50,
  "financial_account_uid": "018df180-7208-727b-a10a-ea545e4a75a8",
  "type": "key",
  "key_type": "email",
  "key": "recipient@example.com",
  "beneficiary": {
    "document_number": "12.345.678/0001-90",
    "name": "Company LTDA"
  },
  "transfer_purpose": "98",
  "external_id": "payment_001"
}
```

**Example Request (by Bank Account):**
```json
{
  "amount": 500.00,
  "financial_account_uid": "018df180-7208-727b-a10a-ea545e4a75a8",
  "type": "bank_account",
  "beneficiary": {
    "document_number": "12.345.678/0001-90",
    "name": "Company LTDA"
  },
  "bank_account": {
    "compe_number": 341,
    "ispb_number": 60701190,
    "agency_number": "1234",
    "agency_digit": "5",
    "account_number": "12345",
    "account_digit": "6",
    "document_number": "12.345.678/0001-90"
  },
  "scheduled_to": "2026-02-01",
  "transfer_purpose": "20"
}
```

### Get Pix Transfer
```
GET /v2/transfer/pix/{uid}
```

### Cancel Pix Transfer
```
PUT /v2/transfer/pix/{uid}/cancel
```

Returns a command object. The cancellation is processed asynchronously.

---

## Transfer Batches

### List Transfer Batches
```
GET /v2/transfer/batches
```

**Query Parameters:**
| Parameter | Type | Description |
|-----------|------|-------------|
| `page` | integer | Page number |
| `per_page` | integer | Items per page (default 50) |

### Create Pix Transfer Batch
```
POST /v2/transfer/pix_batches
```

**Required Parameters:**
| Parameter | Type | Description |
|-----------|------|-------------|
| `financial_account_uid` | string | UUID of the source financial account |
| `transfers` | array | Array of transfer objects or UIDs |

**Example with existing transfers:**
```json
{
  "financial_account_uid": "018df180-7208-727b-a10a-ea545e4a75a8",
  "transfers": [
    { "uid": "019c0cbe-f018-717e-be6e-..." },
    { "uid": "019c0cbe-f018-717e-be6f-..." }
  ]
}
```

**Example with new transfers:**
```json
{
  "financial_account_uid": "018df180-7208-727b-a10a-ea545e4a75a8",
  "transfers": [
    {
      "amount": 100.50,
      "type": "key",
      "key_type": "email",
      "key": "recipient1@example.com",
      "beneficiary": {
        "document_number": "111.222.333-44",
        "name": "João Silva"
      }
    },
    {
      "amount": 200.00,
      "type": "key",
      "key_type": "cpf",
      "key": "222.333.444-55",
      "beneficiary": {
        "document_number": "222.333.444-55",
        "name": "Maria Santos"
      }
    }
  ]
}
```

### Get Transfer Batch
```
GET /v2/transfer/batches/{uid}
```

### Approve Transfer Batch
```
PUT /v2/transfer/batches/{uid}/approve
```

Approves a batch that is in `awaiting_approval` status. Processed asynchronously.

### Reprove Transfer Batch
```
PUT /v2/transfer/batches/{uid}/reprove
```

Reproves (cancels) a batch that is in `awaiting_approval` status. Processed asynchronously.

---

## Response Structure

### Success Response (201 Created - Transfer)
```json
{
  "status": 201,
  "data": {
    "uid": "019c0cbe-f018-717e-be6e-...",
    "status": "pending",
    "registration_status": "pending",
    "financial_account_uid": "018df180-7208-727b-...",
    "amount": 100.50,
    "scheduled_to": "2026-02-01",
    "target": {
      "beneficiary": {
        "document_number": "12.345.678/0001-90",
        "name": "Company LTDA"
      },
      "bank_account": null,
      "internal": null,
      "pix": {
        "pix_type": "key",
        "txid": null,
        "key": "recipient@example.com",
        "key_type": "email",
        "end_to_end_id": null
      },
      "transfer_kind": "pix"
    },
    "source": null,
    "confirmed_at": null,
    "rejected_error": null,
    "rejected_at": null,
    "transaction_code": null,
    "transaction_date": null,
    "transfer_purpose": {
      "code": "98",
      "description": "Pagamentos Diversos"
    },
    "identifier": null,
    "external_id": "payment_001",
    "custom_data": null,
    "tags": [],
    "created_at": "2026-01-30T11:32:45-03:00",
    "updated_at": "2026-01-30T11:32:45-03:00"
  }
}
```

### Success Response (201 Created - Batch)
```json
{
  "status": 201,
  "data": {
    "uid": "019c0cbe-f018-717e-...",
    "status": "pending",
    "registration_status": "pending",
    "financial_account_uid": "018df180-7208-727b-...",
    "transport_kind": "pix",
    "transfers": [
      {
        "uid": "019c0cbe-f018-717e-...",
        "status": "pending",
        "amount": 100.50,
        "target": {...}
      }
    ],
    "created_at": "2026-01-30T11:32:45-03:00",
    "updated_at": "2026-01-30T11:32:45-03:00"
  }
}
```

### List Response (200 OK)
```json
{
  "status": 200,
  "data": [...],
  "pagination": {
    "count": 100,
    "page": 1,
    "items": 50,
    "pages": 2,
    "last": false,
    "next": "/v2/transfer/pix?page=2",
    "prev": null
  }
}
```

---

## Status Reference

### Transfer Status
| Status | Description |
|--------|-------------|
| `pending` | Pending - initial state |
| `awaiting_approval` | Awaiting approval (batch) |
| `approved` | Approved, processing |
| `confirmed` | Confirmed by bank |
| `rejected` | Rejected by bank |
| `reproved` | Reproved (canceled) |

### Registration Status
| Status | Description |
|--------|-------------|
| `pending` | Waiting for registration |
| `requested` | Sent to bank |
| `confirmed` | Confirmed at bank |
| `rejected` | Rejected by bank |
| `failed` | Registration failed |

### Batch Status
| Status | Description |
|--------|-------------|
| `pending` | Pending - initial state |
| `awaiting_approval` | Awaiting manual approval |
| `approved` | Approved, processing |
| `confirmed` | All transfers confirmed |
| `reproved` | Batch reproved |

---

## Transfer Purpose Codes

| Code | Description |
|------|-------------|
| `01` | Billing |
| `03` | Electronic Payment Slip |
| `04` | Bank Reconciliation |
| `05` | Debits |
| `20` | Supplier Payment |
| `22` | Bills, Taxes and Duties Payment |
| `30` | Salary Payment |
| `32` | Fees Payment |
| `33` | Scholarship Payment |
| `40` | Vendor |
| `50` | Insurance Claims Payment |
| `60` | Traveler Expenses |
| `70` | Authorized Payment |
| `75` | Merchant Payment |
| `77` | Compensation Payment |
| `80` | Representatives Payment |
| `90` | Benefits Payment |
| `98` | Miscellaneous Payments (default) |

---

## Key Types

| Type | Description | Format |
|------|-------------|--------|
| `cpf` | Brazilian individual tax ID | `XXX.XXX.XXX-XX` or `XXXXXXXXXXX` |
| `cnpj` | Brazilian company tax ID | `XX.XXX.XXX/XXXX-XX` or `XXXXXXXXXXXXXX` |
| `email` | Email address | `user@example.com` |
| `phone` | Phone number with country code | `+5511999999999` |
| `random` | Random EVP key | UUID format |

---

## Bank Codes (COMPE)

| COMPE | Bank Name |
|-------|-----------|
| 001 | Banco do Brasil |
| 033 | Santander |
| 104 | Caixa Econômica Federal |
| 237 | Bradesco |
| 341 | Itaú Unibanco |
| 422 | Safra |
| 745 | Citibank |
| 756 | Sicoob |

---

## Error Responses

### 401 Unauthorized
```json
{
  "status": 401,
  "errors": [
    {
      "title": "Invalid API Token",
      "code": "unauthorized",
      "detail": "The API Token is different for each Server/URL"
    }
  ]
}
```

### 403 Forbidden
```json
{
  "status": 403,
  "errors": [
    {
      "title": "Scope required: read write",
      "code": "forbidden"
    }
  ]
}
```

### 404 Not Found
```json
{
  "status": 404,
  "errors": [
    {
      "title": "Record not found",
      "code": "not_found",
      "detail": "This record does not exist or was deleted"
    }
  ]
}
```

### 422 Validation Error
```json
{
  "status": 422,
  "errors": [
    {
      "code": "validation_error",
      "param": "amount",
      "detail": "Amount must be greater than 0"
    },
    {
      "code": "validation_error",
      "param": "target",
      "detail": "Bank account ISPB does not match COMPE"
    }
  ]
}
```

---

## Webhooks

Configure webhooks to receive real-time notifications.

### Transfer Events
| Event | Description |
|-------|-------------|
| `transfer.db.created` | Transfer created in database |
| `transfer.pix.cancel.requested` | Cancellation requested |
| `transfer.pix.cancel.confirmed` | Cancellation confirmed |

### Batch Events
| Event | Description |
|-------|-------------|
| `transfer.batch.db.created` | Batch created in database |
| `transfer.pix_batch.confirmed` | Batch confirmed at bank |
| `transfer.transfer_batch.approve.requested` | Approval requested |
| `transfer.transfer_batch.approve.confirmed` | Approval confirmed |
| `transfer.transfer_batch.reprove.requested` | Reproval requested |
| `transfer.transfer_batch.reprove.confirmed` | Reproval confirmed |

### Webhook Payload
```json
{
  "event": "transfer.pix_batch.confirmed",
  "data": {
    "uid": "019c0cbe-f018-717e-...",
    "status": "confirmed",
    "registration_status": "confirmed",
    "transfers": [
      {
        "uid": "019c0cbe-f018-717e-...",
        "status": "confirmed",
        "end_to_end_id": "E12345678..."
      }
    ]
  }
}
```

---

## Additional Resources

- **API Documentation**: https://developers.kobana.com.br
- **API Specification**: https://github.com/universokobana/kobana-api-specs
- **Central Bank Pix Documentation**: https://www.bcb.gov.br/estabilidadefinanceira/pix
