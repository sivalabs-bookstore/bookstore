# Delivery Service
The delivery-service manages order delivery process.

## Event Handlers
1. ORDER_CREATED event handler: 
   * Save order details with status="READY_TO_SHIP"

## Jobs:
1. OrderDeliveryJob:
    * Starts processing orders with status="READY_TO_SHIP"
    * deliver the order (after certain delay) and publish ORDER_DELIVERED event.
    * In case of any failures publish ORDER_ERROR event.
