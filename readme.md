

# 🪙 ZenoPay Float Transfer API Documentation

The **Float Transfer API** allows merchants or partners to move funds between ZenoPay accounts securely using a two-step process — **initiation** and **confirmation**.
All requests must include a valid `x-api-key` in the request header.

---

## 🔐 Authentication

All endpoints require the header:

```
x-api-key: YOUR_API_KEY
```

Make sure your API key has the correct permissions to perform float transfers.

---

## 1️⃣ Initiate Float Transfer

**Endpoint:**
`POST https://zenoapi.com/api/payments/float-transfer/initiate/`

### Description

Initiates a float transfer from the authenticated account to a recipient’s ZenoPay account.
This call returns a `transfer_reference`, which must be used in the **confirmation** step.

---

### Request Headers

| Header      | Type   | Required | Description          |
| ----------- | ------ | -------- | -------------------- |
| `x-api-key` | string | ✅        | Your ZenoPay API key |

---

### Request Body

```json
{
  "recipient_account_id": "zp56799",
  "amount": 2000,
  "note": "For lunch"
}
```

| Parameter              | Type   | Required | Description                                          |
| ---------------------- | ------ | -------- | ---------------------------------------------------- |
| `recipient_account_id` | string | ✅        | ZenoPay account ID of the receiver                   |
| `amount`               | number | ✅        | Amount to transfer (in TZS or your account currency) |
| `note`                 | string | Optional | Optional message or reference for the transfer       |

---

### Example Response

```json
{
  "success": true,
  "message": "Float transfer initiated successfully.",
  "transfer_reference": "d101d683-8a51-4223-bb31-ef2ba6081126"
}
```

| Field                | Type    | Description                                                            |
| -------------------- | ------- | ---------------------------------------------------------------------- |
| `transfer_reference` | string  | Unique identifier for the pending transfer — required for confirmation |
| `message`            | string  | Confirmation message                                                   |
| `success`            | boolean | Indicates if the initiation was successful                             |

---

## 2️⃣ Confirm Float Transfer

**Endpoint:**
`POST https://zenoapi.com/api/payments/float-transfer/confirm/`

### Description

Confirms a float transfer using the reference returned from the initiation call and a valid user PIN.

---

### Request Headers

| Header      | Type   | Required | Description          |
| ----------- | ------ | -------- | -------------------- |
| `x-api-key` | string | ✅        | Your ZenoPay API key |

---

### Request Body

```json
{
  "transfer_reference": "d101d683-8a51-4223-bb31-ef2ba6081126",
  "pin": "2022"
}
```

| Parameter            | Type   | Required | Description                              |
| -------------------- | ------ | -------- | ---------------------------------------- |
| `transfer_reference` | string | ✅        | Reference ID received from initiation    |
| `pin`                | string | ✅        | Authorized transaction PIN of the sender |

---

### Example Response

```json
{
  "success": true,
  "message": "Float transfer confirmed successfully.",
  "transaction_id": "TXN-841119928",
  "status": "Completed"
}
```

| Field            | Type    | Description                                          |
| ---------------- | ------- | ---------------------------------------------------- |
| `transaction_id` | string  | Unique ID of the completed transaction               |
| `status`         | string  | Status of the transfer (e.g., `Completed`, `Failed`) |
| `message`        | string  | Confirmation message                                 |
| `success`        | boolean | Indicates if the confirmation was successful         |

---

## 🚦 Flow Summary

1. **Initiate** → `/float-transfer/initiate/`

   * Generates a `transfer_reference`
2. **Confirm** → `/float-transfer/confirm/`

   * Completes the transfer using the `transfer_reference` and user `pin`

---

## ⚠️ Error Responses

| HTTP Code | Example                                       | Meaning                                |
| --------- | --------------------------------------------- | -------------------------------------- |
| `400`     | `{ "error": "Invalid account ID" }`           | Missing or incorrect recipient account |
| `401`     | `{ "error": "Invalid API key" }`              | Unauthorized request                   |
| `403`     | `{ "error": "Invalid PIN" }`                  | PIN authentication failed              |
| `404`     | `{ "error": "Transfer reference not found" }` | Reference expired or invalid           |
| `500`     | `{ "error": "Internal server error" }`        | Unexpected error from ZenoPay servers  |

---
