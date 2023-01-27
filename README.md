# BookStore
BookStore is an online shopping application selling books.

## Goal
The main goal of building this application is to learn various technologies by building a reasonably complex application.

## Technical Architecture
The key components of BookStore application are:
* **bookstore-webapp:** This is the customer facing web application where they can browse through available books, add books to cart and place an order.
* **bookstore-backoffice:** This is a backoffice web application used by administrators and staff to setup the books catalog and manage orders.
* **backing-services:** These are domain-oriented backend services that fulfill the needs for bookstore-webapp and bookstore-backoffice.

We would like to build the backing-services as independently deployable microservices so that each service can be developed in the preferred language/framework.
Most of these backing-services will expose REST APIs to be consumed by webapp, backoffice and also by other APIs. 
Some backing-services could be only event processors where they consume events from an event store(Kafka, RabbitMQ etc), process them and optionally publish other events.

The web applications bookstore-webapp, bookstore-backoffice could be implemented using SPA frameworks like Angular, VueJS, ReactJS etc 
or using server-side rendering technologies like Thymeleaf.

![SivaLabs BookStore](images/sivalabs-bookstore.png "SivaLabs BookStore")

## Typical application flow
A typical application usage flow looks like:
* Customer browse through product catalog
* Add preferred products to cart
* Go to checkout page
* Enter customer details, delivery address, payment details.
* Place order
  * Validate order details
  * Validate Payment details
  * If payment details is valid then set status to "NEW" otherwise to "ERROR"
  * Save order details in DB
  * Publish OrderCreated Event
  * If payment details is invalid then Publish OrderError Event
* Order Processing
  * Order-service receives OrderCreated Event and send an email notification to customer
  * Order-service receives OrderError Event and send an email notification to customer
  * Delivery-service receives OrderCreated Event and save order with status READY_TO_SHIP
  * Delivery-service DeliveryJob update local status from READY_TO_SHIP to DELIVERED and then publish OrderDelivered Event
  * Order-service receives OrderDelivered Event and update status to DELIVERED
  * Order-service receives OrderDelivered Event and send an email notification to customer
* Customer receives order update emails with a link to view current order details.

## Backend Services

### 1. catalog-service
This service manages the books catalog and exposes the following REST API endpoints:
* Get books by page
* Get book by code(ISBN)
* Create new Book
* Update existing book
* Delete a book
* Search books by title or description

For more info see [Catalog Service Docs](catalog-service.md)

### 2. payment-service
This services exposes API to validate payment info (credit card number, cvv, expiry month/year)

For more info see [Payment Service Docs](payment-service.md)

### 3. order-service
This service manages customer carts and orders and exposes the following REST API endpoints:
* Cart APIs
  * Create a new cart.
  * Add item to cart
  * Update quantity of an item in a cart
  * Remove item from a cart
  * Delete a cart
* Order APIs
  * Create an order. After successfully saving order info publish ORDER_CREATED event.
  * Cancel order. After cancelling order publish ORDER_CANCELLED event.
  * Get all orders
  * Get order by order_number

#### Order Event Processing Handlers:

* ORDER_DELIVERED event handler: Update order status to DELIVERED
* ORDER_CANCELLED event handler: Update order status to CANCELLED
* ORDER_ERROR event handler: Update order status to ERROR

#### Order Event Notification Handlers:

* ORDER_CREATED event handler: Send order received email notification
* ORDER_CANCELLED event handler: Send order cancelled email notification
* ORDER_DELIVERED event handler: Send order delivered email notification
* ORDER_ERROR event handler: Send order can't be fulfilled email notification

For more info see [Order Service Docs](order-service.md)

### 4. delivery-service
This service manages order delivery process.

Event Handlers:
* ORDER_CREATED event handler: save order with status READY_TO_SHIP

Jobs:
* OrderDeliveryJob:
  * Process orders with status=READY_TO_SHIP, deliver the order and publish ORDER_DELIVERED event. 
  * In case of any failures publish ORDER_ERROR event.

For more info see [Delivery Service Docs](delivery-service.md)

Error Response Type:

In Spring Boot 3, the ProblemDetails is a standard response used to provide a consistent format for error responses in RESTful APIs. The ProblemDetails response is defined by the RFC 7807 standard, which specifies a JSON format for error responses.

The ProblemDetails response typically includes the following fields:

 - **type**: a URI that identifies the problem type. This can be used by clients to handle specific types of errors.
 - **title**: a short, human-readable summary of the problem.
 - **status**: the HTTP status code of the response.
 - **detail**: a human-readable explanation of the problem.
 - **instance**: a URI that identifies the specific occurrence of the problem.
 - **properties** : Custom properties what you would like to add.

Sample Response :

```json
{
    "type": "https://api.bookmarks.com/errors/not-found",
    "title": "Bookmark Not Found",
    "status": 404,
    "detail": "Bookmark with id: 111 not found",
    "instance": "/api/bookmarks/111",
    "errorCategory": "Generic",
    "timestamp": "2022-11-30T05:21:59.828411Z"
}
```

For More Details refer [this](https://www.sivalabs.in/spring-boot-3-error-reporting-using-problem-details/) blog post


## How to contribute?
* Run the application and let us know if you face any issue
* Review the code and add your review comments
* Contribute implementation of a service in your favourite language/framework
