# Payment Service
The payment-services exposes API to validate payment info (credit card number, cvv, expiry month/year)

## API Endpoints

### 1. Validate Payment Info
* Endpoint : POST /api/payments/validate
* Security: N/A
* Request Body:
    ```json
    {
      "cardNumber": "1234123412341234", // mandatory
      "cvv": "123", // mandatory
      "expiryMonth": 2, // mandatory
      "expiryYear": 2025 // mandatory
    }
    ```
* Success Response:
    * Status Code: 200
    * Body:
    ```json
    {
      "status": "APPROVED | REJECTED"
    }
    ```
