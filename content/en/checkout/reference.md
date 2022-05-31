---
title: Reference
description: ""
position: 33
category: Checkout
---

## API

### `/checkout`

`POST`

Creates a Checkout Session.

#### Parameters:

<!-- TODO: Required Vs Optional, it's unclear -->
<!-- TODO: Better way to show this! -->

An object with the following properties:
- `type` (*required*): `Array` of `string`s representing which [payment type(s)](/concepts/payment-types) this Checkout session should accept. For now, only `['single']` is supported.
- `payment` (*required*): `Object` describing the payment configuration, with the following properties:
  - `methods`: `Array` of `string`s with the payment methods accepted in this Checkout session. The available ones are:
    - `'cc'` (Credit card)
    - `'mbw'` (MB WAY)
    - `'mb'` (Multibanco)
    - `'dd'` (Direct Debit)
    - `'vi'` (Virtual IBAN)
    - `'uf'` (Universo Flex)
    - `'sc'` (Santander Consumer)
  - `type`: `string` indicating the type of payment for Credit Card and MB WAY operations. Either `'sale'` (default) or `'authorisation'`.
  - `capture`: `Object` required if the payment `type` is sale.
    - `transaction_key`: `string` (<= 50 characters) with the internal key used to identify this transaction in the merchant's database.
    - `capture_date`: `string` in the format `'Y-m-d'` with the capture date.
    - `account`: optional `Object` for multi-account clients.
      - `id`: `string` with the uuid of the account.
    - `splits`: optional `Array` of `Object`s used in split payments. Each `Object` has the following properties:
      - `split_key`: `string` (<= 50 characters) with the merchant's internal key for identifying the split.
      - `split_descriptive`: `string` (<= 255 characters) with the description of the split.
      - `value` (*required*): `number` the split funds, rounded to 2 decimals.
      - `account`: `Object`, the account owner of the split:
        - `id`: `string` with the uuid of the account.
      - `margin_value`: `number` the margin funds, rounded to 2 decimals.
      - `margin_account`: `Object` with the account used for the margin:
        - `id`: `string` with the uuid of the account.
    - `descriptive` (*required*): `string` (<= 255 characters) with the description of the capture. Will be displayed in the bank statement or the MB WAY application.
  - `expiration_time`: Optional `string` in the format `'Y-m-d H:i'` with the last possible time to make the payment. Applicable in Multibanco payments and Virtual IBAN. **TODO** any more?
  - `currency`: `string` with the currency. Available currencies are `'EUR'` (default) and `'BRL'`.
  - `key`: `string` (<= 50 characters) with the merchant's key for identifying the payment.
  - `sdd_mandate`: `Object` *required* if `method` is `dd` (Direct Debit):
    - `id`: Optional `string` to identify this Mandate.
    - `iban` (*required*): `string` (<= 34 characters).
    - `key`: `string` (<= 255 characters) with the merchant's key to identify this Mandate.
    - `name` (*required*): `string` (<= 100 characters).
    - `email` (*required*): `string` (<= 50 characters).
    - `phone` (*required*): `string` (<= 20 characters).
    - `account_holder` (*required*): `string` (<= 100 characters) with the name of the Bank account holder.
    - `country_code`: `string` Bank account country code.
    - `max_num_debits`: Optional `string` with the maximum number of debits allowed in the SDD Mandate.
  <!-- TODO: after we support frequent/sub. - `max_value`: `number` the maximum total value of funds transferred. -->
  <!-- TODO: after we support frequent/sub. How is this used? - `min_value`: -->
  <!-- TODO: after we support frequent/sub. `unlimited_payments`: `boolean` (default `true`) unlimited transactions, `max_value` and `min_value` are per transaction. -->
  <!-- TODO: after we support frequent/sub. - `frequency`: `string` one of `'1D'`, `'1W'`, `'2W'`, `'1M'`, `'2M'`, `'3M'`, `'4M'`, `'6M'`, `'1Y'`, `'2Y'`, `'3Y'`, -->
  <!-- TODO: only for frequent/sub? - `max_captures`: *required* when no `expiration_time` is set. -->
  <!-- TODO: after we support frequent/sub. - `start_time`: `string` in the format `'Y-m-d H:i'`, defining the start of billing cycles. -->
  <!-- TODO: after we support frequent/sub. - `failover`: `boolean` (default `false`) After all retries failed, the payment cycle can have another try with another `single` method. -->
  <!-- TODO: after we support frequent/sub. - `capture_now`: `boolean` (default `false`) whether to schedule an immediate capture and schedule the second one for `start_time`. -->
  <!-- TODO: only for frequent/sub? - `retries`: `number` (default `0`) Number of retries in each payment cycle. -->
- `order` (*required*): `Object` representing the order/cart being paid.
  - `items`: `Array` of `Object`s with the following properties:
    - `key`: `string` with the merchant's key to identify the item.
    - `description`: `string` with a description of the item.
    - `quantity`: `number` of this item being paid.
    - `value`: `number`, the price (rounded to 2 decimals) being paid for the specific item(s).
  - `key`: `string` with the merchant's key to identify the order.
  - `value` (*required*): `number`, the price (rounded to 2 decimals) being paid for the entire order.
- `config`: Optional `Object` with possible UI configurations:
  - `hide_details`: `boolean` (default `false`) If `customer` is given and non-empty, skip the customer details form and show a summary with the given information.
  - `language`: `string` indicating the display language. Supported languages are: `'en'` and `'pt_PT'`. <!-- TODO consider changing to pt-PT (ISO)? -->
    If not sent, a default language will be selected according to the customer's browser language.
  - `logo_url`: `string` with a merchant logo to show above the Checkout form.
- `customer` (*required*):
  - `id`: Optional `string` with the uuid of a previously created customer.
  - `name` (*required*): `string` (<= 255 characters) with the customer's name.
  - `email` (*required*): `string` (<= 70 characters) with the customer's email.
  - `phone` (*required*): `string` (<= 15 characters) with the customer's phone number.
  - `phone_indicative`: `string` (<= 5 characters) with the phone country code.
  - `fiscal_number`: `string` (<= 20 characters) with the customer's fiscal number (prefixed by the country code, e.g. `PT123456789`)
  - `key`: `string` (<= 255 characters) with the merchant's key to identify the customer.
  <!-- TODO: unused for now? - `language`: -->
 
#### Possible responses:

- 201 Created
- 400 Bad Request
- 403 Forbidden
- 429 Too Many Requests
- 500 Internal Error

## SDK

### `startCheckout(manifest, [options])`

#### Parameters:
- `manifest`: The return object from the [checkout service](#checkout).
- `options`: An optional object containing any of the following properties:
  | Option      | Type       | Required | Default            | Description                                                                   |
  | ----------- | ---------- | -------- | ------------------ | ----------------------------------------------------------------------------- |
  | `id`        | `string`   | no       | `easypay-checkout` | The id of the HTML element where the Checkout form should be included.        |
  | `onSuccess` | `function` | no       | `() => {}`         | Callback function to be called when the Checkout is finished succesfully.     |
  | `onError`   | `function` | no       | `() => {}`         | Callback function to be called on errors.                                     |
  | `testing`   | `boolean`  | no       | `false`            | Whether to use the testing API (`true`) or the production one (`false`).      |

#### Return:
- A `CheckoutInstance` object, containing the following method:
  - `unmount()`: Removes all Checkout form content and event listener.

#### Success handler:

`onSuccess(checkoutPaymentInfo)`

Receives an object with the following property:

| Property    | Type       | Description                                                                                                                                 |
| ----------- | ---------- | ------------------------------------------------------------------------------------------------------------------------------------------- |
| `paid`      | `boolean`  | Whether the payment was completed (for synchronous methods, e.g. credit card) or not (for asynchronous methods, e.g. Multibanco reference). |

#### Error handler:

`onError(error)`

Receives an object with the following property:

| Property    | Type       | Description                                                               |
| ----------- | ---------- | ------------------------------------------------------------------------- |
| `code`      | `string `  | The type of error that occurred. See the table below for possible values. |

The error `code` has the following possible values and recommended solutions:

| Value              | Cause                              | Recommended solution                                                                                                                 |
| ------------------ | ---------------------------------- | ------------------------------------------------------------------------------------------------------------------------------------ |
| `checkout-expired` | The Checkout session has expired.  | Create a new Checkout session with the server-to-server call and use the newly returned Manifest to instantiate a new Checkout form. |
| `already-paid`     | The Checkout was already paid.     | Refresh the order information and confirm that it was paid. Give feedback to the user accordingly.                                   |
| `generic-error`    | An unspecified error occurred.     | Since the root cause is unclear, you can try creating a new session or signal an error to the user.                                  |