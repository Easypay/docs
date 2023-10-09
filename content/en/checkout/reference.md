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

- `type`: `Array` with a `string` representing which [payment type](/concepts/payment-types) this Checkout session should accept. Either `['single']`, `['frequent']` or `['subscription']` are supported.  
  Only the first value in the `Array` is considered (e.g. `['single', 'frequent']` would be equivalent to `['single']`). (*required*)
- `payment`: `Object` describing the payment configuration. (*required*)
  <details><summary>Expand</summary>

  - `methods`: `Array` of `string`s with the payment methods accepted in this Checkout session. (*required*)

    The available methods are:
      - `'cc'` (Credit Card)
      - `'mbw'` (MB WAY; *only available for **single** or **frequent** payments*)
      - `'mb'` (Multibanco; *only available for **single** or **frequent** payments*)
      - `'dd'` (Direct Debit)
      - `'vi'` (Virtual IBAN; *only available for **single** or **frequent** payments*)
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
    **Note**: If the expiration time is less than the default checkout session expiration (which is 30 minutes), the user won't be able to interact with the Checkout after that time.
  - `currency`: `string` with the currency. Available currencies are `'EUR'` (*default*) and `'BRL'`.
  - `key`: `string` (<= 50 characters) with the merchant's key for identifying the payment.
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
  | Option           | Type       | Required | Default              | Description                                                                |
  | ---------------- | ---------- | -------- | -------------------- | -------------------------------------------------------------------------- |
  | `id`             | `string`   | no       | `'easypay-checkout'` | The id of the HTML element where the Checkout form should be included.     |
  | `onSuccess`      | `function` | no       | `() => {}`           | Callback function to be called when the Checkout is finished successfully. |
  | `onError`        | `function` | no       | `() => {}`           | Callback function to be called on (unrecoverable) errors.                  |
  | `onPaymentError` | `function` | no       | `() => {}`           | Callback function to be called on (recoverable) payment errors.            |
  | `onClose`        | `function` | no       | `undefined`          | Callback function to be called when the Checkout interaction is closed.    |
  | `testing`        | `boolean`  | no       | `false`              | Whether to use the testing API (`true`) or the production one (`false`).   |
  | `display`(1)     | `string`   | no       | `'inline'`           | The display style of the element that hosts the Checkout.                  |
  | `hideDetails`    | `boolean`  | no       | `false` | Whether to hide the details form or not. An expandable summary will be shown with the details, instead. |
  | `language`(2)    | `string`   | no       | `undefined`          | The language in which to display the Checkout.                             |
  | `logoUrl`               | `string`  | no       | `undefined`     | The merchant logo url to display in the Checkout.                           |
  | `backgroundColor`       | `string`  | no       | `'#ffffff'`     | The color used as the background of the Checkout page.                      |
  | `accentColor`           | `string`  | no       | `'#0d71f9'`     | The color used in highlights, as well as default buttons and input borders. |
  | `errorColor`            | `string`  | no       | `'#ff151f'`     | The color used for errors.                                                  |
  | `inputBackgroundColor`  | `string`  | no       | `'transparent'` | The color used for the input backgrounds.                                   |
  | `inputBorderColor`      | `string`  | no       | *accentColor*   | The color for input borders.                                                |
  | `inputBorderRadius`     | `number`  | no       | `50`            | The border radius for inputs, in `px`.                                      |
  | `inputFloatingLabel`    | `boolean` | no       | `true`          | Whether inputs should use floating labels.                                  |
  | `buttonBackgroundColor` | `string`  | no       | *accentColor*   | The color used for the button backgrounds.                                  |
  | `buttonBorderRadius`    | `number`  | no       | `50`            | The border radius for buttons, in `px`.                                     |
  | `buttonBoxShadow`       | `boolean` | no       | `true`          | Whether the buttons should have box-shadow.                                 |
  | `fontFamily`            | `string`  | no       | `'Overpass'`    | The font used for the text.                                                 |
  | `baseFontSize`          | `number`  | no       | `10`            | The value in `px` for the font size of the root element (`1rem`).           |

  ##### Options

  (1) `display` available values: `inline` (default) or `popup`.  
  (2) `language` available values: `en` or `pt_PT`. If left `undefined`, a default language will be selected according to the customer's browser language.

#### Return:

- A `CheckoutInstance` object, containing the following method:
  - `unmount()`: Removes all Checkout form content and event listener.

<br>

#### Success handler:

`onSuccess(checkoutInfo)`

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
| `cardType`       | Credit Card  | `string`  | The credit card type (`'VISA'` or `'MasterCard'`).                               |
| `lastFour`       | Credit Card  | `string`  | The last four digits of the credit card.                                         |
| `cardCountryCode`| Credit Card  | `string`  | The country code of the credit card.                                             |
| `expirationDate` (3) | Credit Card / Multibanco   | `string`  | The expiration date of the card (Credit Card) or the payment (Multibanco).   |
| `entity`         | Multibanco   | `string`  | The Multibanco entity.                                                           |
| `reference`      | Multibanco   | `string`  | The Multibanco reference.                                                        |
| `sddMandate` (4) | Direct Debit | `Object`  | SEPA Direct Debit mandate.                                                       |

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

(3) Format of the expiration varies between Credit Card (`'MM/YY'` format) and Multibanco (`'Y-m-d H:i'`).

(4) Properties of the `sddMandate` Object:
<details><summary>Expand</summary>

| Property        | Type     | Description                                                           |
| --------------- | -------- | --------------------------------------------------------------------- |
| `accountHolder` | `string` | Name of the account holder.                                           |
| `billingEntity` | `string` | The billing entity for the payments.                                  |
| `countryCode`   | `string` | Country code of the bank account.                                     |
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

<br>

#### Payment error handler:

`onPaymentError(error)`

Signals the occurrence of a recoverable error during a payment attempt. These errors are informative and for logging/analysis purposes, as the user is allowed (and encouraged) to try the payment again with the same or other payment method.

Receives an object with the following properties:

| Property        | Type     | Description                                                               |
| --------------- | -------- | ------------------------------------------------------------------------- |
| `code`          | `string` | The type of error that occurred. See the table below for possible values. |
| `paymentMethod` | `string` | The payment method for which the error happened.                          |
| `checkout`      | `object` | On `payment-failure` errors, the Checkout object containing the payment information that had already been created. Has the same properties as the [onSuccess](/checkout/reference#success-handler) `checkoutInfo` object. |

The error `code` has the following possible values and recommended solutions:

| Value             | Cause                                                      | Recommended solution                           |
| ----------------- | ---------------------------------------------------------- | ---------------------------------------------- |
| `generic-error`   | There was an error before the payment process had started. | Allow the user to try again. If problem persists, report it to easypay.                                                       |
| `payment-failure` | There was an error after the payment process had started.  | Allow the user to try again. If problem persists, use the attached `checkout` object to get details about the payment intent. |

#### Examples of each appearance property

##### Changing the logo

```js
const checkoutInstance = easypayCheckout.startCheckout(manifest, {
  logoUrl: 'www.example.com/mylogo.png'
})
```

Outcome:

![Checkout](/checkout/withlogo.png)

##### Changing the background color


```js
const checkoutInstance = easypayCheckout.startCheckout(manifest, {
  backgroundColor: '#222222'
})
```

The text colors are automatically adjusted to shades of black or white depending on the background color, in order to achieve the best contrast possible.

Outcome:

![Checkout](/checkout/withbgcolor.png)


##### Changing the accent color

```js
const checkoutInstance = easypayCheckout.startCheckout(manifest, {
  accentColor: '#ffa861'
})
```

The text colors (over the accent, such as in buttons) are automatically adjusted to black or white, in order to achieve the best contrast possible.

Outcome:

![Checkout](/checkout/withaccentcolor.png)


##### Changing the error color

```js
const checkoutInstance = easypayCheckout.startCheckout(manifest, {
  errorColor: '#d18800'
})
```

Outcome:

![Checkout](/checkout/witherrorcolor.png)


##### Changing the input background color

```js
const checkoutInstance = easypayCheckout.startCheckout(manifest, {
  inputBackgroundColor: '#001f3d'
})
```

Outcome:

![Checkout](/checkout/withinputbgcolor.png)


##### Changing the input border color

```js
const checkoutInstance = easypayCheckout.startCheckout(manifest, {
  inputBorderColor: '#000000'
})
```

Outcome:

![Checkout](/checkout/withinputbordercolor.png)


##### Changing the input border radius

```js
const checkoutInstance = easypayCheckout.startCheckout(manifest, {
  inputBorderRadius: 0
})
```

Outcome:

![Checkout](/checkout/withinputborderradius.png)


##### Using non-floating labels

```js
const checkoutInstance = easypayCheckout.startCheckout(manifest, {
  inputFloatingLabel: false
})
```

Outcome:

![Checkout](/checkout/withoutfloatinglabels.png)


##### Changing button background color

```js
const checkoutInstance = easypayCheckout.startCheckout(manifest, {
  buttonBackgroundColor: '#000000'
})
```

The button text color is automatically adjusted to black or white, in order to achieve the best contrast possible.

Outcome:

![Checkout](/checkout/withbuttonbgcolor.png)


##### Changing button border radius

```js
const checkoutInstance = easypayCheckout.startCheckout(manifest, {
  buttonBorderRadius: 0
})
```

Outcome:

![Checkout](/checkout/withbuttonborderradius.png)


##### Disabling button box-shadow

```js
const checkoutInstance = easypayCheckout.startCheckout(manifest, {
  buttonBoxShadow: false
})
```

Outcome:

![Checkout](/checkout/withoutbuttonboxshadow.png)


##### Changing font family

```js
const checkoutInstance = easypayCheckout.startCheckout(manifest, {
  fontFamily: 'cursive'
})
```

The `fontFamily` property can have three types of values:
- Generic family name (e.g. `'sans-serif'`, `'serif'`, `'cursive'`)
- Web safe font (e.g. `'arial'`, `'verdana'`)
- Web font URL (e.g. `'https://fonts.gstatic.com/s/overpass/v12/qFda35WCmI96Ajtm83upeyoaX6QPnlo6_PPbM5qKl8Kuo8AzesE.woff2'`, must include the protocol `http`/`https`)

Outcome:

![Checkout](/checkout/withfontfamily.png)


##### Changing base font size

```js
const checkoutInstance = easypayCheckout.startCheckout(manifest, {
  baseFontSize: 8
})
```

Changes the value in `px` for the font size of the root element (`1rem`). The default value is `10`, so any value below will reduce the font size, while a value above will increase it.

Besides changing the size of the text (while keeping the proportions), this setting changes most of the layout dimensions, effectively increasing or decreasing the scale.

Outcome:

![Checkout](/checkout/withbasefontsize.png)