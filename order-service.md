# Order Service
The order-service manages customer orders and exposes the following REST API endpoints:

## API Endpoints

### 1. Create an order
* Endpoint : POST /api/orders
* Security: N/A
* Request Body:
    ```json
    {
      "items": [
          {
              "productCode": "ABCDEFGH", // unique
              "name": "Book Title", // mandatory
              "price": 24.50, // mandatory
              "quantity": 1
          },
          {
              "productCode": "JKLMNOP", // unique
              "name": "Book Title", // mandatory
              "price": 20.50, // mandatory
              "quantity": 2
          }
      ],
      "customer": {
        "name":"customer name", // mandatory
        "email": "customer@email.com", // mandatory
        "phone": "999999999" // mandatory
      },
      "deliveryAddress": {
          "addressLine1": "street", // mandatory
          "addressLine2": "area",
          "city": "city", // mandatory
          "state": "state", // mandatory
          "zipCode": "zipcode", // mandatory
          "country": "Country" // mandatory
      },
      "payment": {
          "cardNumber": "1234123412341234", // mandatory
          "cvv": "123", // mandatory
          "expiryMonth": 2, // mandatory
          "expiryYear": 2025 // mandatory
      }
    }
    ```
* Success Response:
    * Status Code: 202
    * Body:
    ```json
    {
       "orderId":"abcd-efgh-ijkl-mnop",
       "status": "NEW"
    }
    ```
* Error Response - Bad Request
    * Reasons: Missing required fields, ISBN not exists, Payment rejected.
    * Status Code: 400
    * Body:
    ```json
      {
          "message": "Payment rejected"
      } 
    ```
### 2. Cancel order
* Endpoint : DELETE /api/orders/{orderId}
* Security: Header `Authorization: Bearer <JWT_TOKEN>` with Role ADMIN or STAFF
* Success Response:
    * Status Code: 200
    * Body:
    ```json
    {
       "orderId":"abcd-efgh-ijkl-mnop",
       "status": "CANCELLED"
    }
    ```
* Error Response - Bad Request
    * Reasons: Missing required fields, orderId not exists, Order already delivered.
    * Status Code: 400
    * Body:
    ```json
      {
          "message": "orderId not exists"
      } 
    ```
### 3. Get all orders
* Endpoint : GET /api/orders
* Security: Header `Authorization: Bearer <JWT_TOKEN>` with Role ADMIN or STAFF
* Success Response:
    * Status Code: 200
    * Body:
    ```json
      {
         "totalElements": 25,
         "totalPages": 3,
         "currentPage": 1,
         "data": [  
            {
               "orderId":"abcd-efgh-ijkl-mnop",
               "status": "NEW",
               "items": [
                  {
                      "productCode": "ABCDEFGH",
                      "name": "Book Title",
                      "price": 24.50,
                      "quantity": 1
                  },
                  {
                      "productCode": "JKLMNOP",
                      "name": "Book Title",
                      "price": 20.50,
                      "quantity": 2
                  }
              ],
              "customer": {
                "name":"customer name",
                "email": "customer@email.com",
                "phone": "999999999"
              },
              "deliveryAddress": {
                  "addressLine1": "street",
                  "addressLine2": "area",
                  "city": "city",
                  "state": "state",
                  "zipCode": "zipcode",
                  "country": "Country"
              }
            }
        ]
    }
    ```
### 4. Get order by order_id
* Endpoint : GET /api/orders/{orderId}
* Security: N/A
* Success Response:
    * Status Code: 200
    * Body:
    ```json
    {
       "orderId":"abcd-efgh-ijkl-mnop",
       "status": "NEW",
       "items": [
          {
              "productCode": "ABCDEFGH",
              "name": "Book Title",
              "price": 24.50,
              "quantity": 1
          },
          {
              "productCode": "JKLMNOP",
              "name": "Book Title",
              "price": 20.50,
              "quantity": 2
          }
      ],
      "customer": {
        "name":"customer name",
        "email": "customer@email.com",
        "phone": "999999999"
      },
      "deliveryAddress": {
          "addressLine1": "street",
          "addressLine2": "area",
          "city": "city",
          "state": "state",
          "zipCode": "zipcode",
          "country": "Country"
      }
    }
    ```
* Error Response - Not Found
    * Status Code: 404
    * Body:
     ```json
    {
        "message": "Order with id <order_id> not found"
    } 
     ```
## Events
1. ORDER_SHIPPING event handler: 
    * Update order status to "SHIPPING"

2. ORDER_DELIVERED event handler: 
    * Update order status to "DELIVERED"

3. ORDER_ERROR event handler: 
    * Update order status to "ERROR"

## Jobs
1. Order Processing Job
    * When an Order is placed and Payment is Accepted then Order details will be stored in DB with status="NEW".
    * This job runs at regular intervals and process every order with status="NEW"
    * Each new order details will be published with OrderCreatedEvent 
