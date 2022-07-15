---
title: Notifications / Webhooks
description: ""
position: 42
category: API
---

## What are notifications and why they are useful
Easypay use notifications (webhooks) for event notifications.

Notifications are **POST API** calls (JSON encoded) that let your application know an event has happened.

This is a server-to-server service that sends near real-time notifications, informing about a payment status change.

Notifications are particularly useful for asynchronous events like:

* when a customer pays a MB reference.
* a frequent payment succeeds/fails.
* subscription succeeds/fails, among other useful events.
* a checkout single sale payment succeeds/fails.

With notifications your system can automate custom actions in response to the transitions that happen in a payment flow.


## Supported Notification types

Easypay supports 3 notification types:

- **Generic notification**: a notification informing about all state transitions of some payment resource.
- **Authorisation notification**: a notification informing about some payment transitioning to authorization status. Available for single and frequent payments.
- **Transaction notification**: a notification informing about some payment transitioning to capture status. Available for single and frequent payments.

As an example, if you subscribe to Generic and Authorisation notifications, we will inform you about the payment status transition to authorized in two different formats and you will receive two notifications for the same event.

What varies in these two notifications, for the same event, is the contract of the notification message.

## Generic Notification

All transitions that happen in a payment flow are notified via generic notification.

The notification flow follows the following diagram:

![Recommended flow](/notifications/notification_recommended_flow.png)

1. A change of payment state is notified to the configured generic notification endpoint.
2. The integrator system that received a generic notification, queries Easypay API's. This is a recommended to ensure that the received notification was issued by Easypay.
3. The integrator system performs its internal application logic regarding the payment state transition, according to API response.

The generic notification message contains the following fields:
```json
{
	"id": "5eca7446-14e9-47bb-aabb-5ee237159b8b",
	"key": "dcf9ab3fd95ca3d5607853f36d46f161c8715858",
	"type": "capture",
	"status": "success",
	"messages": ["Your request was successfully captured"],
	"date": "2022-08-10 14:56:54"
}
```

### Payload Attributes

| Name | Type | Description |
| ---  | ---- | ----------- |
| **type**| string | the type of payment status change.
| **status**| string| the status of the given payment status change.
| **messages**| string| messages associated with this payment status change.
| **date**| string| the expiration associated with this single or frequent payment. Follows the format <Y-m-d H:i> If none was given, defaults to empty.he merchant identification key provided on a single or frequent payment request.

In a generic notification, the fields `id` and `key` will change according to the **payment type** and **operation**.

The following table describes the possible combinations:

|Payment Type | Name  | Type | Description|
|------------| ---- | ----| --- |
| **Single Authorisation** | **id** <br /> **key** | string  <br /> string | the `id` of the single payment.<br /> the field `key` given to create single request payload.
| **Single Capture** | **id** <br /> **key** | string  <br /> string | the `id` of the single payment.<br />the field `transaction_key` given to capturing single payload.
| **Single Sale** | **id** <br /> **key** | string  <br /> string | the `id` of the single payment. <br />the field `transaction_key`, inside of object `capture`, given to create single request payload.
| **Frequent Create** | **id** <br />**key** | string  <br /> string | the `id` of the frequent payment.<br />the field `key` given to create frequent request payload.
| **Frequent Authorization** | **id** <br />**key** | string  <br /> string | the `id` of the frequent payment.<br />the field `transaction_key` given to authorisation frequent request payload.
| **Frequent Capture** | **id** <br />**key** | string  <br /> string | the `id` of the capture operation. <br />the field `transaction_key` given to capture frequent request payload.
| **Refund Single/Frequent Capture** | **id** <br />**key**  | string <br /> string| the `id` of the refund.<br />the field `transaction_key` given to refund capture request payload.
| **Void Single/Frequent Authorisation** | **id** <br />**key**  | string <br /> string| the `id` of the void.<br />the field `transaction_key` given to void authorisation request payload.
| **Subcription Create** | **id** <br />**key**  | string <br /> string| the `id` of the subscription.<br />the field `key` given to create subscription request payload.
| **Subcription Capture** | **id** <br />**key**  | string <br /> string| the `id` of the subscription.<br />the field `transaction_key` inside of object `capture`, given to create subscription request payload.
| **Chargeback Single** | **id** <br />**key**  | string <br /> string| the `id` of the single.<br />the field `transaction_key` given to create frequent request payload.
| **Chargeback Frequent** | **id** <br />**key**  | string <br /> string| the `id` of the capture operation.<br />the field `transaction_key` given to capture frequent request payload.
| **Outpayment** | **id** <br />**key**  | string <br /> string| the `id` of the outpayment operation.<br />the field `key` given to create outpayment request payload.


**The complete generic notification reference can be found [here](https://api.prod.easypay.pt/docs#tag/Notification/paths/~1your-generic-notification-endpoint/post)**.

## Authorisation Notification Message structure

The delivered authorisation notification message contains the following fields:
```json
{
  "id": "1bbc14c3-8ca8-492c-887d-1ca86400e4fa",
  "value": 1,
  "currency": "EUR",
  "key": "the merchant key",
  "expiration_time": "2022-01-01 10:20",
  "customer": {
    "id": "22ea3cc9-424b-489a-91b7-8955f643dc93",
    "name": "Customer Example",
    "email": "customer@example.com",
    "phone": "911234567",
    "phone_indicative": "+351",
    "fiscal_number": "PT123456789",
    "key": "Key Example",
    "language": "PT"
  },
  "method": "mb",
  "account": {
    "id": "4c67e74b-a256-4e0a-965d-97bf5d01bd50"
  },
  "authorisation": {
    "id": "4c67e74b-a256-4e0a-965d-97bf5d01bd50"
  }
}
```

### Attributes

| Name | Type | Description |
| ---  | ---- | ----------- |
| **id**| string| the uuid of a single or frequent payment
| **value**| double | the amount requested on single/frequent creation. Rounded to 2 decimals.
| **key**| string| the merchant identification key provided on a single or frequent payment request
| **expiration_time**| string| the expiration associated with this single or frequent payment. Follows the format <Y-m-d H:i> If none was given, defaults to empty.
| **customer**| object| the customer associated at single or frequent payment request
| **method**| string| the choosen payment method associated with single or frequent payment
| **account**| object| the account from which this notification was originated
| **authorization**| object| the authorization details.


**The complete authorisation notification reference can be found [here](https://api.prod.easypay.pt/docs#tag/Notification/paths/~1your-authorisation-notification-endpoint/post)**.

## Transaction Notification Message structure

The transaction notification message contains the following fields:
```json
{
	"id": "87615356-0a88-42bd-8abb-aab3e90128de",
	"value": "40",
	"currency": "EUR",
	"key": "the merchant key",
	"expiration_time": "2023-08-07 20:00",
	"method": "MBW",
	"customer": {
		"id": "2eb64a7f-90a7-4dc6-959b-1d9aba44910c",
		"phone": "910410419",
	},
	"account": {
		"id": "0b8de6e7-89c8-4d76-93e8-019bc058f27d"
	},
	"transaction": {
		"id": "eb23923b-3529-4b71-b54e-1e707a8d55c4",
		"key": "transaction_key_of_this_capture",
		"type": "capture",
		"date": "2022-08-10T12:45:50Z",
		"values": {
			"requested": "40",
			"paid": "40",
			"fixed_fee": "0",
			"variable_fee": "0",
			"tax": "0",
			"transfer": "0"
		}
	}
}
```

### Attributes

| Name | Type | Description |
| ---  | ---- | ----------- |
| **id**| string| the uuid of a single or frequent payment
| **value**| double | the amount requested on single/frequent creation. Rounded to 2 decimals.
| **currency**| string | the currency of requested amount requested on single/frequent creation.
| **key**| string| the merchant identification key provided on a single or frequent payment request.
| **expiration_time**| string| the expiration associated with this single or frequent payment. Follows the format <Y-m-d H:i> If none was given, defaults to empty.
| **method**| string| the choosen payment method associated with single or frequent payment.
| **customer**| object| the customer associated at single or frequent payment request.
| **account**| object| the account from which this notification was originated.
| **transaction**| object| the details of this transaction.


**The complete transaction notification reference can be found [here](https://api.prod.easypay.pt/docs#tag/Notification/paths/~1your-notification-endpoint/post)**.

## Configuring Notifications

On Easypay backoffice, navigate to `Web Services > Configuration API 2.0`.

Select the payment account you want to receive notifications.

Select `Notifications`.

To receive generic notifications, place your system url on `Generic - URL `.

To receive authorisation notifications, place your system url on `Authorisation - URL`.

To receive capture notifications, place your system url on `Payment - URL`.

## Following Notification via Backoffice

On Easypay backoffice, navigate to `Web Services > Notifications API 2.0`.

There, you can select the payment account whose notifications you are interested in.


## Notification Reference

You can find more information about Easypay notifications [here](https://api.prod.easypay.pt/docs#tag/Notification).
