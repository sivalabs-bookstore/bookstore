# Promotion Service
The promotion-service manages promotions(discounts) for books and exposes the following REST API endpoints:

## API Endpoints

### 1. Get promotions for a given set of productCodes(ISBNs)
* Endpoint : GET /api/promotions?productCodes=ISBN_1,ISBN_2,ISBN_3,ISBN_N
* Security: N/A
* Response:
    * Status Code: 200
    * Body:
      ```json
      [
            {
                "productCode": "ISBN_1",
                "discount": 1.50
            },
            {
                "productCode": "ISBN_3",
                "discount": 3.50
            }
      ]
      ```
### 2. Get promotion for a given productCode(ISBN)
* Endpoint : GET /api/promotions/{productCode}
* Security: N/A
* Success Response:
    * Status Code: 200
    * Body:
      ```json
      {
         "productCode": "ISBN",
         "discount": 1.50 // 0 if no promotion exists for given ISBN
      }
      ```
### 3. Create or Update promotion for a given book
* Endpoint : PUT /api/promotions
* Security: Header `Authorization: Bearer <JWT_TOKEN>` with Role ADMIN or STAFF
  * Request Body:
    ```json
      {
        "productCode": "ABCDEFGH", // mandatory
        "discount": 24.50 // mandatory
      }
      ```
* Success Response:
    * Status Code: 200
    * Body:
    ```json
    {
      "productCode": "ABCDEFGH",
      "discount": 24.50
    }
    ```
* Error Response - Bad Request
    * Reasons: Missing required fields.
    * Status Code: 400
    * Body:
    ```json
      {
          "message": "ISBN is required"
      } 
    ```
### 4. Delete promotion for a given book
* Endpoint : DELETE /api/promotions/{productCode}
* Security: Header `Authorization: Bearer <JWT_TOKEN>` with Role ADMIN or STAFF
* Success Response:
    * Status Code: 200
* Error Response - Not Found
    * Reasons: Promotion for ISBN not exist etc.
    * Status Code: 404
    * Body:
    ```json
      {
          "message": "Promotion for ISBN not exist"
      }
    ```
