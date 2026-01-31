# Kobana Payment API - Complete Reference

Complete API documentation for paying Pix charges, decoding QR codes, and managing payment batches.

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

## Pix Decoding

### Decode Pix EMV
```
POST /v2/payment/pix/decode
```

Decode a Pix EMV string (QR code copy-paste) to get the charge information before paying.

**Request Body:**
| Parameter | Type | Required | Description |
|-----------|------|----------|-------------|
| `emv` | string | Yes | The Pix EMV string (copy-paste from QR code) |

**Example Request:**
```json
{
  "emv": "00020126580014br.gov.bcb.pix0136123e4567-e89b-12d3-a456-426614174000520400005303986540550.005802BR5925EMPRESA LTDA6009SAO PAULO62070503***6304ABCD"
}
```

**Success Response (200 OK):**
```json
{
  "status": 200,
  "data": {
    "type": "dynamic",
    "key": "123e4567-e89b-12d3-a456-426614174000",
    "key_type": "random",
    "merchant_name": "EMPRESA LTDA",
    "merchant_city": "SAO PAULO",
    "amount": 50.00,
    "txid": "PAYMENT123456789",
    "description": "Invoice January 2026",
    "due_date": "2026-02-10",
    "original_amount": 50.00,
    "fine": {
      "type": "percentage",
      "value": 2.0
    },
    "interest": {
      "type": "monthly_percentage",
      "value": 1.0
    },
    "discount": {
      "type": "fixed_amount",
      "value": 5.00,
      "until": "2026-02-05"
    },
    "payer": {
      "document_number": "111.222.333-44",
      "name": "João da Silva"
    },
    "beneficiary": {
      "document_number": "12.345.678/0001-90",
      "name": "EMPRESA LTDA"
    }
  }
}
```

---

## Pix Payments

### List Pix Payments
```
GET /v2/payment/pix
```

**Query Parameters:**
| Parameter | Type | Description |
|-----------|------|-------------|
| `page` | integer | Page number |
| `per_page` | integer | Items per page (default 50, max 50) |
| `status` | string | Filter by status |
| `registration_status` | string | Filter by registration status |
| `financial_account_uid` | string | Filter by financial account |
| `created_from` | date | Created date from (YYYY-MM-DD) |
| `created_to` | date | Created date to |
| `scheduled_from` | date | Scheduled date from |
| `scheduled_to` | date | Scheduled date to |
| `external_id` | string | Filter by external ID |
| `tags` | string | Filter by tags (comma-separated) |

### Create Pix Payment
```
POST /v2/payment/pix
```

**Required Parameters:**
| Parameter | Type | Description |
|-----------|------|-------------|
| `financial_account_uid` | string | UUID of the source financial account |
| `amount` | decimal | Payment amount in BRL |

**Payment by EMV (Dynamic Pix):**
| Parameter | Type | Description |
|-----------|------|-------------|
| `emv` | string | Pix EMV string (from QR code) |

**Payment by Pix Key (Static Pix):**
| Parameter | Type | Description |
|-----------|------|-------------|
| `type` | string | Must be `key` |
| `key_type` | string | `cpf`, `cnpj`, `email`, `phone`, `random` |
| `key` | string | The Pix key value |
| `beneficiary.document_number` | string | Beneficiary CPF/CNPJ |
| `beneficiary.name` | string | Beneficiary name |

**Optional Parameters:**
| Parameter | Type | Description |
|-----------|------|-------------|
| `scheduled_to` | date | Schedule date (YYYY-MM-DD) |
| `description` | string | Payment description |
| `identifier` | string | Payment identifier (bank-specific) |
| `external_id` | string | External ID for tracking |
| `custom_data` | object | Custom metadata (JSON) |
| `tags` | array | Tags for organization |

**Example Request (by EMV):**
```json
{
  "financial_account_uid": "018df180-7208-727b-a10a-ea545e4a75a8",
  "emv": "00020126580014br.gov.bcb.pix0136123e4567-e89b-12d3-a456-426614174000...",
  "amount": 150.00,
  "scheduled_to": "2026-02-01",
  "external_id": "payment_001",
  "description": "Payment for Invoice INV-2026-001"
}
```

**Example Request (by Pix Key):**
```json
{
  "financial_account_uid": "018df180-7208-727b-a10a-ea545e4a75a8",
  "type": "key",
  "key_type": "email",
  "key": "recipient@example.com",
  "amount": 150.00,
  "beneficiary": {
    "document_number": "12.345.678/0001-90",
    "name": "Company LTDA"
  },
  "description": "Payment for services",
  "external_id": "payment_002"
}
```

### Get Pix Payment
```
GET /v2/payment/pix/{uid}
```

**Path Parameters:**
| Parameter | Type | Description |
|-----------|------|-------------|
| `uid` | string | Payment UID |

**Query Parameters:**
| Parameter | Type | Description |
|-----------|------|-------------|
| `field` | string | Alternative field: `id`, `external_id` |

### Cancel Pix Payment
```
PUT /v2/payment/pix/{uid}/cancel
```

Returns a command object. The cancellation is processed asynchronously.

Only pending payments can be canceled.

---

## Payment Batches

### List Payment Batches
```
GET /v2/payment/batches
```

**Query Parameters:**
| Parameter | Type | Description |
|-----------|------|-------------|
| `page` | integer | Page number |
| `per_page` | integer | Items per page (default 50) |
| `financial_account_uid` | string | Filter by financial account |

### Create Pix Payment Batch
```
POST /v2/payment/pix_batches
```

**Required Parameters:**
| Parameter | Type | Description |
|-----------|------|-------------|
| `financial_account_uid` | string | UUID of the source financial account |
| `payments` | array | Array of payment objects or UIDs |

**Example with existing payments:**
```json
{
  "financial_account_uid": "018df180-7208-727b-a10a-ea545e4a75a8",
  "payments": [
    { "uid": "019c0cbe-f018-717e-be6e-..." },
    { "uid": "019c0cbe-f018-717e-be6f-..." }
  ]
}
```

**Example with new payments:**
```json
{
  "financial_account_uid": "018df180-7208-727b-a10a-ea545e4a75a8",
  "payments": [
    {
      "emv": "00020126580014br.gov.bcb.pix...",
      "amount": 150.00,
      "external_id": "payment_001"
    },
    {
      "type": "key",
      "key_type": "cpf",
      "key": "111.222.333-44",
      "amount": 200.00,
      "beneficiary": {
        "document_number": "111.222.333-44",
        "name": "João Silva"
      }
    }
  ]
}
```

### Get Payment Batch
```
GET /v2/payment/batches/{uid}
```

### Approve Payment Batch
```
PUT /v2/payment/batches/{uid}/approve
```

Approves a batch that is in `awaiting_approval` status. Processed asynchronously.

### Reprove Payment Batch
```
PUT /v2/payment/batches/{uid}/reprove
```

Reproves (cancels) a batch that is in `awaiting_approval` status. Processed asynchronously.

---

## Response Structure

### Success Response (201 Created - Payment)
```json
{
  "status": 201,
  "data": {
    "uid": "019c0cbe-f018-717e-be6e-...",
    "status": "pending",
    "registration_status": "pending",
    "financial_account_uid": "018df180-7208-727b-...",
    "amount": 150.00,
    "scheduled_to": "2026-02-01",
    "target": {
      "beneficiary": {
        "document_number": "12.345.678/0001-90",
        "name": "Company LTDA"
      },
      "pix": {
        "pix_type": "dynamic",
        "txid": "PAYMENT123456789",
        "key": "123e4567-e89b-12d3-...",
        "key_type": "random",
        "end_to_end_id": null
      },
      "payment_kind": "pix"
    },
    "source": null,
    "confirmed_at": null,
    "rejected_error": null,
    "rejected_at": null,
    "transaction_code": null,
    "transaction_date": null,
    "description": "Payment for Invoice INV-2026-001",
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
    "payments": [
      {
        "uid": "019c0cbe-f018-717e-...",
        "status": "pending",
        "amount": 150.00,
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
    "next": "/v2/payment/pix?page=2",
    "prev": null
  }
}
```

---

## Status Reference

### Payment Status
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
| `confirmed` | All payments confirmed |
| `reproved` | Batch reproved |

---

## Pix Types

| Type | Description |
|------|-------------|
| `static` | Static Pix - Fixed key, variable amount |
| `dynamic` | Dynamic Pix - Generated for specific charge |
| `dynamic_due_date` | Dynamic Pix with due date (Pix Cobrança) |

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
      "param": "emv",
      "detail": "Invalid EMV format"
    },
    {
      "code": "validation_error",
      "param": "amount",
      "detail": "Amount must match the charge amount"
    }
  ]
}
```

---

## Webhooks

Configure webhooks to receive real-time notifications.

### Payment Events
| Event | Description |
|-------|-------------|
| `payment.db.created` | Payment created in database |
| `payment.pix.requested` | Payment sent to bank |
| `payment.pix.confirmed` | Payment confirmed at bank |
| `payment.pix.rejected` | Payment rejected by bank |
| `payment.pix.cancel.requested` | Cancellation requested |
| `payment.pix.cancel.confirmed` | Cancellation confirmed |

### Batch Events
| Event | Description |
|-------|-------------|
| `payment.batch.db.created` | Batch created in database |
| `payment.pix_batch.confirmed` | Batch confirmed at bank |
| `payment.batch.approve.requested` | Approval requested |
| `payment.batch.approve.confirmed` | Approval confirmed |
| `payment.batch.reprove.requested` | Reproval requested |
| `payment.batch.reprove.confirmed` | Reproval confirmed |

### Webhook Payload
```json
{
  "event": "payment.pix.confirmed",
  "data": {
    "uid": "019c0cbe-f018-717e-...",
    "status": "confirmed",
    "registration_status": "confirmed",
    "amount": 150.00,
    "target": {
      "pix": {
        "end_to_end_id": "E12345678..."
      }
    }
  }
}
```

---

## About Pix Payments

Pix payments allow you to pay charges generated by third parties using QR codes or copy-paste strings (EMV). This is different from Pix transfers, which send money directly to a Pix key.

### Use Cases
- **Pay invoices** - Scan QR code from bills and invoices
- **Pay suppliers** - Use copy-paste from received charges
- **Pay utilities** - QR codes from utility bills
- **Pay e-commerce** - Checkout Pix payments

### Payment Flow
1. **Receive the Pix** - Get QR code or copy-paste string
2. **Decode (optional)** - Validate the charge data
3. **Create payment** - Submit to Kobana API
4. **Create batch** - Send to bank for processing
5. **Confirm** - Payment is confirmed

---

## Additional Resources

- **API Documentation**: https://developers.kobana.com.br
- **API Specification**: https://github.com/universokobana/kobana-api-specs
- **Central Bank Pix Documentation**: https://www.bcb.gov.br/estabilidadefinanceira/pix
