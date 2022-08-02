---
title: Reference
description: ""
position: 33
category: Checkout
---

## API

[API URLs](/api/overview#urls)

### Create Checkout session

`POST` `/checkout`

Creates a Checkout Session. 

#### Parameters:

An object with the following properties:

<div class="ep-protocol-list">

- `type`: `Array` with a `string` representing which [payment type](/concepts/payment-types) this Checkout session should accept. Either `['single']` or `['frequent']` are supported.  
  Only the first value in the `Array` is considered (e.g. `['single', 'frequent']` would be equivalent to `['single']`). (*required*)
- `payment`: `Object` describing the payment configuration. (*required*)
  <details><summary>Expand</summary>

  - `methods`: `Array` of `string`s with the payment methods accepted in this Checkout session. (*required*)

    The available methods are:
      - `'cc'` (Credit Card)
      - `'mbw'` (MB WAY)
      - `'mb'` (Multibanco)
      - `'dd'` (Direct Debit)
      - `'vi'` (Virtual IBAN; *only available for **single** payments*)
      - `'uf'` (Universo Flex; *only available for **single** payments*)
      - `'sc'` (Santander Consumer; *only available for **single** payments*)
  - `type`: `string` indicating the type of payment for Credit Card and MB WAY operations. Either `'sale'` (*default*) or `'authorisation'`.
  - `capture`: `Object`, *required* if the payment `type` is sale.
    <details><summary>Expand</summary>
    
    - `transaction_key`: `string` (<= 50 characters) with the internal key used to identify this transaction in the merchant's database.
    - `capture_date`: `string` in the format `'Y-m-d'` with the capture date.
    - `account`: `Object` for multi-account clients.
      - `id`: `string` with the uuid of the account.
    - `splits`: `Array` of `Object`s used in split payments. Each `Object` has the following properties:
      - `split_key`: `string` (<= 50 characters) with the merchant's internal key for identifying the split.
      - `split_descriptive`: `string` (<= 255 characters) with the description of the split.
      - `value`: `number` the split funds, rounded to 2 decimals. (*required*)
      - `account`: `Object`, the account owner of the split:
        - `id`: `string` with the uuid of the account.
      - `margin_value`: `number` the margin funds, rounded to 2 decimals.
      - `margin_account`: `Object` with the account used for the margin:
        - `id`: `string` with the uuid of the account.
    - `descriptive`: `string` (<= 255 characters) with the description of the capture. Will be displayed in the bank statement or in the MB WAY application. (*required*)
    </details>
  - `expiration_time`: `string` in the format `'Y-m-d H:i'` with the last possible time to make the payment. Applicable in Multibanco payments.  
    **Note**: This does not affect checkout session expiration (which is 30 minutes).
  - `currency`: `string` with the currency. Available currencies are `'EUR'` (*default*) and `'BRL'`.
  - `key`: `string` (<= 50 characters) with the merchant's key for identifying the payment.
  - `sdd_mandate`: `Object` *required* if `method` is `dd` (Direct Debit):
    <details><summary>Expand</summary>

    - `id`: `string` to identify this Mandate.
    - `iban`: `string` (<= 34 characters). (*required*)
    - `key`: `string` (<= 255 characters) with the merchant's key to identify this Mandate.
    - `name`: `string` (<= 100 characters). (*required*)
    - `email`: `string` (<= 50 characters). (*required*)
    - `phone`: `string` (<= 20 characters). (*required*)
    - `account_holder`: `string` (<= 100 characters) with the name of the Bank account holder. (*required*)
    - `country_code`: `string` Bank account country code.
    - `max_num_debits`: `string` with the maximum number of debits allowed in the SDD Mandate.
    </details>
  <br>

  Additional options for ***frequent*** payments:
  - `max_value`: `number` the maximum total value of funds to be transferred.
  - `min_value`: `number` the minimum total value of funds to be transferred.
  - `unlimited_payments`: `boolean` (*default* `true`) unlimited transactions. Uses `max_value` and `min_value` as limits per transaction instead.
  <br>
  
  Additional options for ***subscriptions***:
  - `frequency`: `string` one of `'1D'`, `'1W'`, `'2W'`, `'1M'`, `'2M'`, `'3M'`, `'4M'`, `'6M'`, `'1Y'`, `'2Y'`, `'3Y'`. (*required*)
  - `max_captures`: `number` of payments (*required* when no `expiration_time` is set).
  - `start_time`: `string` in the format `'Y-m-d H:i'`, defining the start of billing cycles. (*required*)
  - `capture_now`: `boolean` (default `false`) Whether to schedule an immediate capture and schedule the second one for `start_time`.
  - `retries`: `number` (default `0`) Number of retries in each payment cycle.
  - `failover`: `boolean` (default `false`) After all retries failed, the payment cycle can have another try with another `single` method.
  </details>
- `order`: `Object` representing the order/cart being paid. (*required*)
  <details><summary>Expand</summary>
  
  - `items`: `Array` of `Object`s with the following properties:
    - `key`: `string` with the merchant's key to identify the item.
    - `description`: `string` with a description of the item.
    - `quantity`: `number` of this item being paid.
    - `value`: `number`, the price being paid for the specific item(s), rounded to 2 decimals.
  - `key`: `string` with the merchant's key to identify the order.
  - `value`: `number`, the price being paid for the entire order, rounded to 2 decimals. (*required*)
  </details>
- `customer`: `Object` with the customer details. (*required*)
  <details><summary>Expand</summary>

  - `id`: `string` with the uuid of a previously created customer.
  - `name`: `string` (<= 255 characters) with the customer's name. (*required*)
  - `email`: `string` (<= 70 characters) with the customer's email. (*required*)
  - `phone`: `string` (<= 15 characters) with the customer's phone number. (*required*)
  - `phone_indicative`: `string` (<= 5 characters) with the phone country code.
  - `fiscal_number`: `string` (<= 20 characters) with the customer's fiscal number (prefixed by the country code, e.g. `PT123456789`)
  - `key`: `string` (<= 255 characters) with the merchant's key to identify the customer.
  </details>

</div>

#### Possible responses:

- 201 Created
- 400 Bad Request
- 403 Forbidden
- 429 Too Many Requests
- 500 Internal Error

#### Return value

Returns a JSON Object (referred to as a *Checkout Manifest*) with the following properties:

<div class="ep-protocol-list">

- `id`: `string` with the Checkout session id.
- `session`: `string` with a session token to be used by the rest of the Checkout process.
- `config`: *deprecated* `Object` with configuration properties.

</div>

## SDK

### `startCheckout(manifest, [options])`

#### Parameters:

- `manifest`: The return object from the [checkout service](#create-checkout-session).
- `options`: An optional object containing any of the following properties:
  | Option        | Type       | Required | Default            | Description                                                                |
  | ------------- | ---------- | -------- | ------------------ | -------------------------------------------------------------------------- |
  | `id`          | `string`   | no       | `easypay-checkout` | The id of the HTML element where the Checkout form should be included.     |
  | `onSuccess`   | `function` | no       | `() => {}`         | Callback function to be called when the Checkout is finished successfully. |
  | `onError`     | `function` | no       | `() => {}`         | Callback function to be called on errors.                                  |
  | `onClose`     | `function` | no       | `undefined`        | Callback function to be called when the Checkout popup is closed.          |
  | `testing`     | `boolean`  | no       | `false`            | Whether to use the testing API (`true`) or the production one (`false`).   |
  | `display`(1)  | `string`   | no       | `inline`           | The display style of the element that hosts the Checkout.                  |
  | `hideDetails` | `boolean`  | no       | `false`            | Whether to hide the details form or not. An expandable summary will be shown with the details, instead. |
  | `language`(2) | `string`   | no       | `undefined`        | The language in which to display the Checkout.                             |
  | `logoUrl`     | `string`   | no       | `undefined`        | The merchant logo url to display in the Checkout.                          |

  ##### Options

  (1) `display` available values: `inline` (default) or `popup`.  
  (2) `language` available values: `en` or `pt_PT`. If left `undefined`, a default language will be selected according to the customer's browser language.

#### Return:

- A `CheckoutInstance` object, containing the following method:
  - `unmount()`: Removes all Checkout form content and event listener.

<br>

#### Success handler:

`onSuccess(successInfo)`

Receives an object with the following properties:

| Property  | Type      | Description                                                                        |
| --------- | --------- | ---------------------------------------------------------------------------------- |
| `id`      | `string`  | The id of the Checkout session.                                                    |
| `type`    | `string`  | The payment type for this Checkout (`'single'`, `'frequent'` or `'subscription'`). |
| `payment` | `Object`  | Detailed information about the payment.                                            |

Properties of the `payment` Object:

**Note**: Some properties only appear with certain payment methods.

| Property         | Method       | Type      | Description                                                                      |
| ---------------- | ------------ | --------- | -------------------------------------------------------------------------------- |
| `id`             | All          | `string`  | The payment's id.                                                                |
| `method` (1)     | All          | `string`  | The payment method chosen by the customer.                                       |
| `status` (2)     | All          | `Object`  | The status of the payment.                                                       |
| `value`          | All          | `number`  | The order value, rounded to two decimal places. Not used in `frequent` payments. |
| `entity`         | Multibanco   | `string`  | The Multibanco entity.                                                           |
| `reference`      | Multibanco   | `string`  | The Multibanco reference.                                                        |
| `expirationDate` | Multibanco   | `string`  | The expiration date for the payment.                                             |
| `sddMandate` (3) | Direct Debit | `Object`  | SEPA Direct Debit mandate.                                                       |

(1) Possible method values are the same as in the [Checkout creation](/checkout/reference#create-checkout-session).

(2) Possible payment status values:
<details><summary>Expand</summary>

- `'authorised'`
- `'deleted'`
- `'enrolled'`
- `'error'`
- `'failed'`
- `'paid'`
- `'pending'`
- `'success'`
- `'tokenized'` (To be used later in `frequent` payments.)
- `'voided'`
</details>

(3) Properties of the `sddMandate` Object:
<details><summary>Expand</summary>

| Property        | Type     | Description                                                           |
| --------------- | -------- | --------------------------------------------------------------------- |
| `accountHolder` | `string` | Name of the account holder.                                           |
| `billingEntity` | `string` | The billing entity for the payments.                                  |
| `countryCode`   | `string` | Country code of the phone number.                                     |
| `email`         | `string` | The customer's e-mail address.                                        |
| `iban`          | `string` | The IBAN.                                                             |
| `id`            | `string` | The mandate's id.                                                     |
| `maxNumDebits`  | `string` | The maximum number of debits allowed for this Direct Debit.           |
| `name`          | `string` | The customer's name. May be different from the account holder's name. |
| `phone`         | `string` | The customer's phone number.                                          |
| `referenceAdc`  | `string` | The authorization reference.                                          |

</details>

<br>

#### Error handler:

`onError(error)`

Receives an object with the following property:

| Property | Type      | Description                                                               |
| -------- | --------- | ------------------------------------------------------------------------- |
| `code`   | `string ` | The type of error that occurred. See the table below for possible values. |

The error `code` has the following possible values and recommended solutions:

| Value              | Cause                             | Recommended solution                                                                                                                 |
| ------------------ | --------------------------------- | ------------------------------------------------------------------------------------------------------------------------------------ |
| `checkout-expired` | The Checkout session has expired. | Create a new Checkout session with the server-to-server call and use the newly returned Manifest to instantiate a new Checkout form. |
| `already-paid`     | The Checkout was already paid.    | Refresh the order information and confirm that it was paid. Give feedback to the user accordingly.                                   |
| `checkout-canceled`     | The Checkout was canceled.    | Create a new Checkout session with the server-to-server call and use the newly returned Manifest to instantiate a new Checkout form.                                   |
| `generic-error`    | An unspecified error occurred.    | Since the root cause is unclear, you can try creating a new session or signal an error to the user.                                  |
