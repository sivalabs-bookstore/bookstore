# Catalog Service
The catalog-service manages the books catalog and exposes the following REST API endpoints:

## API Endpoints

### 1. Get books by page
* Endpoint : GET /api/products?page=1
* Security: N/A
* Response:
  * Status Code: 200
  * Body:
    ```json
    {
       "totalElements": 124,
       "totalPages": 13,
       "currentPage": 1,
       "data": [
          {
              "id": "uuid",
              "code": "ABCDEFGH",
              "name": "Book Title",
              "description": "book description",
              "image_url": "https://images.com/1.png",
              "price": 24.50,
              "discount": 1.50,
              "salePrice": 23.0
          }
       ]
    }
    ```
### 2. Get book by code(ISBN)
* Endpoint : GET /api/products/{code}
* Security: N/A
* Success Response:
    * Status Code: 200
    * Body:
    ```json
    {
      "id": "uuid",
      "code": "ABCDEFGH",
      "name": "Book Title",
      "description": "book description",
      "image_url": "https://images.com/1.png",
      "price": 24.50,
      "discount": 1.50,
      "salePrice": 23.0
    }
    ```
* Error Response - Not Found
    * Status Code: 404
    * Body:
    ```json
      {
          "message": "Product with code <ISBN> not found"
      } 
    ```
### 3. Search books by title or description
* Endpoint : GET /api/products/search?query=keyword&page=1
* Security: N/A
* Response:
    * Status Code: 200
    * Body:
      ```json
      {
         "totalElements": 114,
         "totalPages": 12,
         "currentPage": 1,
         "data": [
            {
                "id": "uuid",
                "code": "ABCDEFGH",
                "name": "Book Title",
                "description": "book description",
                "image_url": "https://images.com/1.png",
                "price": 24.50,
                "discount": 1.50,
                "salePrice": 23.0
            }
         ]
      }
      ```

### 4. Create new Book
* Endpoint : POST /api/products
* Security: Header `Authorization: Bearer <JWT_TOKEN>` with Role ADMIN or STAFF
* Request Body:
    ```json
    {
      "code": "ABCDEFGH", // unique
      "name": "Book Title", // mandatory
      "description": "book description", // optional
      "image_url": "https://images.com/1.png",// optional
      "price": 24.50 // mandatory
    }
    ```
* Success Response:
    * Status Code: 201
    * Body:
    ```json
    {
      "id": "uuid",
      "code": "ABCDEFGH",
      "name": "Book Title",
      "description": "book description",
      "image_url": "https://images.com/1.png",
      "price": 24.50
    }
    ```
* Error Response - Bad Request
    * Reasons: Missing required fields, Book with ISBN already exist etc.
    * Status Code: 400
    * Body:
    ```json
      {
          "message": "Product with ISBN already exist"
      } 
    ```
### 5. Update existing book
* Endpoint : PUT /api/products/{code}
* Security: Header `Authorization: Bearer <JWT_TOKEN>` with Role ADMIN or STAFF
* Request Body:
    ```json
    {
      "name": "Book Title", // mandatory
      "description": "book description", // optional
      "image_url": "https://images.com/1.png", // optional
      "price": 24.50 // mandatory
    }
    ```
* Success Response:
    * Status Code: 200
    * Body:
    ```json
    {
      "id": "uuid",
      "code": "ISBN",
      "name": "Book Title",
      "description": "book description",
      "image_url": "https://images.com/1.png",
      "price": 24.50
    }
    ```
* Error Response - Bad Request
    * Reasons: Missing required fields, Book with ISBN not exist etc.
    * Status Code: 400
    * Body:
    ```json
      {
          "message": "Product with ISBN not exist"
      } 
    ```
### 6. Delete a book
* Endpoint : DELETE /api/products/{code}
* Security: Header `Authorization: Bearer <JWT_TOKEN>` with Role ADMIN or STAFF
* Success Response:
    * Status Code: 200
* Error Response - Not Found
    * Reasons: Book with ISBN not exist etc.
    * Status Code: 404
    * Body:
    ```json
      {
          "message": "Product with ISBN not exist"
      } 
    ```
