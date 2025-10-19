

# 🧾 ZenoPay Float Transfer API Documentation

The **Float Transfer API** allows businesses or agents to securely transfer float between ZenoPay accounts. The transfer process is done in **two steps** — `initiate` and `confirm`.

---

## 🔐 Authentication

All requests require an API key in the header:

```
x-api-key: YOUR_API_KEY
```

---

## 🚀 1. Initiate Float Transfer

**Endpoint:**
`POST https://zenoapi.com/api/payments/float-transfer/initiate/`

### Description

This endpoint initiates a float transfer between two ZenoPay accounts.
Once initiated, the transfer will be in a **pending** state until confirmed with a PIN.

---

### Request Headers

| Key          | Value            | Required |
| ------------ | ---------------- | -------- |
| Content-Type | application/json | ✅        |
| x-api-key    | Your API key     | ✅        |

---

### Request Body

```json
{
  "recipient_account_id": "zp56799",
  "amount": 2000,
  "note": "For lunch"
}
```

| Parameter            | Type   | Required | Description                                  |
| -------------------- | ------ | -------- | -------------------------------------------- |
| recipient_account_id | string | ✅        | The ZenoPay account ID of the recipient      |
| amount               | number | ✅        | Amount to transfer (TZS or default currency) |
| note                 | string | ❌        | Optional note or reason for the transfer     |

---

### Example Response

```json
{
  "status": "pending",
  "message": "Transfer initiated. Please confirm with your PIN.",
  "transfer_reference": "8df06a6f-098b-4e27-94ed-1a8a6b2a454a",
  "recipient": "nombo",
  "amount": "2000.00",
  "recipient_account_id": "zp88692522"
}
```

| Field                | Description                                     |
| -------------------- | ----------------------------------------------- |
| status               | Current status of the transfer (`pending`)      |
| message              | Human-readable message describing the next step |
| transfer_reference   | Unique reference ID for confirming the transfer |
| recipient            | Name or identifier of the recipient             |
| amount               | Transfer amount                                 |
| recipient_account_id | The recipient’s ZenoPay account ID              |

---

## ✅ 2. Confirm Float Transfer

**Endpoint:**
`POST https://zenoapi.com/api/payments/float-transfer/confirm/`

### Description

This endpoint confirms a previously initiated float transfer using your account **PIN**.
Once confirmed, the transfer will be processed and funds moved to the recipient account.

---

### Request Headers

| Key          | Value            | Required |
| ------------ | ---------------- | -------- |
| Content-Type | application/json | ✅        |
| x-api-key    | Your API key     | ✅        |

---

### Request Body

```json
{
  "transfer_reference": "d101d683-8a51-4223-bb31-ef2ba6081126",
  "pin": "2022"
}
```

| Parameter          | Type   | Required | Description                                          |
| ------------------ | ------ | -------- | ---------------------------------------------------- |
| transfer_reference | string | ✅        | The reference ID returned from the initiate endpoint |
| pin                | string | ✅        | Your ZenoPay transaction PIN                         |

---

### Example Response

```json
{
  "status": "success",
  "message": "Transfer completed, SMS and email sent.",
  "amount": "2000.00",
  "recipient_phone": null
}
```

| Field           | Description                                        |
| --------------- | -------------------------------------------------- |
| status          | Status of the transfer (`success`, `failed`, etc.) |
| message         | Message indicating result of the operation         |
| amount          | Transferred amount                                 |
| recipient_phone | Recipient’s phone number (if available)            |

---

## ⚠️ Error Responses

| Status Code | Example                                   | Description                            |
| ----------- | ----------------------------------------- | -------------------------------------- |
| 400         | `{"error": "Invalid transfer reference"}` | When the provided reference is invalid |
| 401         | `{"error": "Unauthorized"}`               | Missing or invalid API key             |
| 403         | `{"error": "Invalid PIN"}`                | Incorrect confirmation PIN             |
| 500         | `{"error": "Internal server error"}`      | Something went wrong on the server     |

---

## 💡 Notes

* Always confirm transfers immediately after initiation to avoid expiry.
* Ensure the `x-api-key` is securely stored and **never exposed publicly**.
* The same process applies for **both agent and business accounts**.

