# Cart Service
The cart-services exposes API to manage carts

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
                "productCode": "ISBN_1",
                "name": "title",
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
            "productCode": "ISBN_1",
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
                "productCode": "ISBN_1",
                "name": "title",
                "description": "description",
                "price": 1.50,
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
            "productCode": "ISBN_1",
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
                "productCode": "ISBN_1",
                "name": "title",
                "description": "description",
                "price": 1.50,
                "quantity": 1
            }
          ]
      }
      ```
### 4. Remove item in cart
* Endpoint : DELETE /api/carts/items/{productCode}?cartId=CART_ID
* Security: N/A
* Response:
    * Status Code: 200
    * Body:
      ```json
      {
          "id": "cart_id",
          "items": [
            {
                "productCode": "ISBN_1",
                "name": "title",
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
    