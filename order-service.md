# Order Service
The order-service manages customer orders and exposes the following REST API endpoints:

## API Endpoints


### 1. Get cart for a given cartId
* Endpoint : GET /api/carts<?cartId=CART_ID>
* Security: N/A
* Response:
    * Status Code: 200
    * Body:
      ```json
      {
          "id": "cart_id",
          "items": [
            {
                "code": "ISBN_1",
                "name": "product name",
                "description": "description",
                "price": 1.50,
                "quantity": 1
            }
          ]
      }
      ```

Here cartId query parameter is optional. If not included this request will create a new cart.
If cartId is specified then it will return the cart info for given cartId, if cartId not found then returns 404.

### 2. Add item to cart
* Endpoint : POST /api/carts<?cartId=CART_ID>
* Security: N/A
* Request Body:
    ```json
        {
            "code": "ISBN_1",
            "quantity": 1 //optional, default to 1
        }
    ```
* Response:
    * Status Code: 200
    * Body:
      ```json
      {
          "id": "cart_id",
          "items": [
            {
                "code": "ISBN_1",
                "name": "product name",
                "description": "description",
                "price": 12.50,
                "quantity": 1
            }
          ]
      }
      ```

### 3. Update item quantity in cart
* Endpoint : PUT /api/carts?cartId=CART_ID
* Security: N/A
* Request Body:
    ```json
        {
            "code": "ISBN_1",
            "quantity": 1
        }
    ```
* Response:
    * Status Code: 200
    * Body:
      ```json
      {
          "id": "cart_id",
          "items": [
            {
                "code": "ISBN_1",
                "name": "product name",
                "description": "description",
                "price": 12.50,
                "quantity": 1
            }
          ]
      }
      ```
### 4. Remove item in cart
* Endpoint : DELETE /api/carts/items/{code}?cartId=CART_ID
* Security: N/A
* Response:
    * Status Code: 200
    * Body:
      ```json
      {
          "id": "cart_id",
          "items": [
            {
                "code": "ISBN_1",
                "name": "product name",
                "description": "description",
                "price": 1.50,
                "quantity": 1
            }
          ]
      }
      ```
### 5. Delete cart
* Endpoint : DELETE /api/carts?cartId=CART_ID
* Security: N/A
* Response:
    * Status Code: 200

### 6. Create an order
* Endpoint : POST /api/orders
* Security: N/A
* Request Body:
    ```json
    {
      "items": [
          {
              "code": "ABCDEFGH", // unique
              "name": "Book Title", // mandatory
              "price": 24.50, // mandatory
              "quantity": 1
          },
          {
              "code": "JKLMNOP", // unique
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
### 7. Cancel order
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
### 8. Get all orders
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
                      "code": "ABCDEFGH",
                      "name": "Book Title",
                      "price": 24.50,
                      "quantity": 1
                  },
                  {
                      "code": "JKLMNOP",
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
### 9. Get order by order_id
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
              "code": "ABCDEFGH",
              "name": "Book Title",
              "price": 24.50,
              "quantity": 1
          },
          {
              "code": "JKLMNOP",
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
## Order Event Processing Handlers:

1. ORDER_DELIVERED event handler: 
    * Update order status to "DELIVERED"

2. ORDER_ERROR event handler: 
    * Update order status to "ERROR"

3. ORDER_CANCELLED event handler:
    * Update order status to "CANCELLED"

## Order Event Notification Handlers:
1. ORDER_CREATED event handler:
    * Send order received email notification

2. ORDER_CANCELLED event handler:
    * Send order cancellation email notification

3. ORDER_DELIVERED event handler:
    * Send order delivered email notification

4. ORDER_ERROR event handler:
    * Send order can't be fulfilled email notification

## Jobs
1. Order Processing Job
    * When an Order is placed and Payment is Accepted then Order details will be stored in DB with status="NEW".
    * This job runs at regular intervals and process every order with status="NEW"
    * Each new order details will be published with OrderCreatedEvent 
