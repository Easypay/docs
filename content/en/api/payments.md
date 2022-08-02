---
title: Payments
description: ""
position: 41
category: API
---

## Capture

The capture endpoint allows capturing funds from a Single Payment authorisation, a Frequent Payment or a Frequent Payment authorisation. It also allows getting the details of a previous capture.

### Capture payment

`POST` `/capture/<payment_id>`

#### Parameters

<div class="ep-protocol-list">

  - `transaction_key`: `string` (<= 50 characters) with the internal key used to identify this transaction in the merchant's database.
  - `capture_date`: `string` in the format `'Y-m-d'` with the capture date.
  - `account`: `Object` for multi-account clients.
    - `id`: `string` with the uuid of the account.
  - `splits`: `Array` of `Object`s used in split payments.
    <details><summary>Expand</summary>
      
      Each `Object` has the following properties:
    - `split_key`: `string` (<= 50 characters) with the merchant's internal key for identifying the split.
    - `split_descriptive`: `string` (<= 255 characters) with the description of the split.
    - `value`: `number` the split funds, rounded to 2 decimals. (*required*)
    - `account`: `Object`, the account owner of the split:
      - `id`: `string` with the uuid of the account.
    - `margin_value`: `number` the margin funds, rounded to 2 decimals.
    - `margin_account`: `Object` with the account used for the margin:
      - `id`: `string` with the uuid of the account.

    </details>
  - `force_3ds`: `boolean` (default `false`) Whether or not you want to force the 3DS authentication on Credit Cards (used only on Frequent capture without authorisation).
  - `descriptive`: `string` (<= 255 characters) with the description of the capture. Will be displayed in the bank statement or in the MB WAY application. (*required*)
  - `value`: `number`, the amount of funds being captured, rounded to 2 decimals.

</div>


#### Possible responses

- 201 Created
- 400 Bad Request
- 403 Forbidden
- 412 Precondition Failed
- 422 Unprocessable Entity
- 428 Precondition Required
- 429 Too Many Requests
- 500 Internal Error

#### Return value

Returns a JSON Object with the following properties:

<div class="ep-protocol-list">

  - `status`: `string` with the status of the request (`"ok"` when successful, `"error"` otherwise).
  - `message`: `Array` with a `string` message explaining what happened (e.g. `["Your request was successfully created"]` on success).
  - `id`: `string` with the id of the created capture, on success.
  - `method`: an `Object` with additional information about the payment method
     Used in 3DS authentication with the properties:
     - `type`: `string` with the payment method (e.g. 'CC' for credit card).
     - `url`: `string` with the URL to redirect the user to continue 3DS authentication.

</div>

### Get Capture details

`GET` `/capture/<capture_id>`

#### Possible responses

- 200 Success
- 401 Unauthorized
- 403 Forbidden
- 404 Resource Not Found
- 429 Too Many Requests
- 500 Internal Error

#### Return value

Returns a JSON Object with the following properties:

<div class="ep-protocol-list">

- `id`: `string` with the capture uuid.
- `status`: `string` with the status of the capture. One of: `"delayed"`, `"waiting"`, `"pending"`, `"success"`, `"failed"`, or `"refunded"`.
- `descriptive`: `string` (<= 255 characters) with the description of the capture given in its creation.
- `value`: `number`, the amount of funds captured, rounded to 2 decimals.
- `payment_id`: `string`, the `id` of the associated payment.
- `payment_type`: `string`, either `"single"` or `"frequent"`.
- `account`: account `Object`:
  - `id`: `string` with the account id.
- `splits`: splits `Object` given in the [capture creation](#capture-payment).
- `transaction_key`: `string` (<= 50 characters) with the internal key used to identify this transaction in the merchant's database.
- `capture_date`: `string` in the format `'Y-m-d'` with the capture date.

</div>