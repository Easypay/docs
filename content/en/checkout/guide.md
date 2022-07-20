---
title: Guide
description: ""
position: 32
category: Checkout
---

## Requirements

Before using Checkout, you will need the following:

<!-- Copied from the current Docs.
     TODO: Clearer explanation after the relevant sections are written -->

- An easypay account configured for the new APIs
- A CIN with CCs configured

Refer to [Configuration of Necessary Data](/first-steps/necessary-data) if you're missing any of the above.

An example app that demonstrates the steps described in this guide is available at https://github.com/Easypay/checkout-demo .

## Creating a checkout session

To create a Checkout session, you are required to send a server-to-server `POST` request to the [`/checkout`](/checkout/reference#checkout) endpoint. The created session will have an expiration time of 30 minutes, after which the users won't be able to pay that specific Checkout anymore.

Like all API requests, this request is authenticated as described in [Authentication](/first-steps/necessary-data#authentication), which means you must include the `AccountId` and `ApiKey` headers.

An example payload is:

```json
{
  "type": ["single"],
  "payment": {
    "methods": ["cc", "mb", "mbw", "dd", "vi", "uf", "sc"],
    "type": "sale",
    "capture": {
      "descriptive": "Descriptive Example"
    },
    "currency": "EUR",
    "expiration_time": null
  },
  "order": {
    "items": [
      {
        "description": "T-shirt",
        "quantity": 1,
        "key": "t-shirt",
        "value": 12.5
      },
      {
        "description": "Gloves",
        "quantity": 1,
        "key": "gloves",
        "value": 7
      }
    ],
    "key": "order-key",
    "value": 19.5
  },
  "customer": null
}
```

You can check all the possible parameters in the [Checkout reference](/checkout/reference#checkout).

The service will return something like:

```json
{
  "id": "57cc19e9-f393-4cfa-9516-8807763b5096",
  "session": "8zoaBOC0Mj5Mg_YAbdRCqYGI66Zc2DttAWeyVcedzhuT3r2IE8aS7l7H-sQOyppgawIv-wO48H_oKDHMzvMf1TeIJwo3Xu7HO7Tmj7y1iblt8t9JlEzyNdf5HurTvCeszC8PdRS4mbaPBCMCQ7g4xkG0qUuOEhiee5OB49MJJQ-Mo9vEnC_R4hzRBi-HSIMviTNDcCm5NiMnn__nWZxpfAmh6pYUg_eznqTGNbheYLu7ku_mBn7HvAdJQcgsmk6eeD-Z9S8SsK-ONHpbJQz1LNZsUkIIiWpNxPMrzD5Je0r2veawzAaIQmCeN8hYncHDfl5YZ7PP28MFVtEjMGOxsOsqxL4wmDOLPO5s6aWMq7Dxns-OGgf0r7dmaka4Y2mXovLVtNxsGgsNbBav8K86f5DceCDmPKBzYh-X5i1nx07LuqYKoJmA8NfyVP5tb-R3Y9dtwbPzcHc_AyQVKlsM0HtW3JXAdqoIBfsDgUqwASeuqQHEyIITKlcRvDIzWFaxTgXmmJauj6et7mo1GEM4L1gDkA1IXOd3n4HU2GvfPw==",
  "config": null
}
```

This return object is referred to as a **Checkout manifest**, and will be needed in the steps below.

## Integrating in your page

### Script tag

Include the easypay Checkout SDK in your page:

```html
<script src="https://cdn.easypay.pt/checkout/2.0.0/"></script>
```

Prepare an empty element in your page where you'd like the Checkout form to be and give it the `easypay-checkout` id:

```html
<div id="easypay-checkout"></div>
```

Make sure to trigger the server-to-server call described above and get the contents of the manifest:

```javascript
const manifest = await yourFunctionToGetTheManifest()
```

Then, use the globally available `easypayCheckout` variable to initialize the SDK and display the form:

```javascript
easypayCheckout.startCheckout(manifest)
```

And the Checkout should be running smoothly.

### NPM import

Instead of directly including the JavaScript library from the CDN, you can import it as an NPM module:

```bash
npm install --save @easypaypt/checkout-sdk
```

And use it like any other importable dependency:

```javascript
import { startCheckout } from '@easypaypt/checkout-sdk'

startCheckout(manifest)
```

All the other steps are the same as for the CDN.

### Customizing element id

If you'd like to have the Checkout form displayed in an element with another id, for instance:

```html
<div id="checkout-form"></div>
```

You can pass the id in the options parameter of the Checkout initialization:

```javascript
startCheckout(manifest, {
  id: 'checkout-form',
})
```

### Removing Checkout contents

When starting a Checkout form, you can store the return value to later manage it (particularly by removing it from the page at a later date).

```javascript
const checkoutInstance = startCheckout(manifest)
```

When you no longer wish to display the Checkout contents, call the `unmount()` method on the return object:

```javascript
checkoutInstance.unmount()
```

### Reacting to Checkout completion

It will often be useful to know when users finish the Checkout process, so you can hide the Checkout contents and display a thank you message.
The SDK includes a callback parameter that you can pass to get notified when the Checkout process is succesfully completed.

Your callback function can receive a parameter with additional details about the outcome of the Checkout process. For now, the only additional information is whether the payment was completed (synchronous payment method) or not (asynchronous payment method). You can check the differences in the [payment methods documentation](/concepts/payment-methods).

```javascript
function mySuccessHandler(checkoutPaymentInfo) {
  checkoutInstance.unmount()
  if (checkoutPaymentInfo.paid) {
    document.write('Your payment was received. Thank you.')
  } else {
    document.write('Your order was received and is now awaiting payment. Thank you.')
  }
}

const checkoutInstance = startCheckout(manifest, {
  onSuccess: mySuccessHandler,
})
```

### Reacting to Checkout errors

The Checkout process is resilient to most forms of errors and allows the customer to retry multiple times with different payment methods until the payment is completed.
However, there are cases where the Checkout form is unable to recover on its own, such as an expired Checkout session, an attempt to pay a Checkout that was already paid or a Checkout that was canceled.

You can be notified of such cases by passing the callback parameter `onError`:

```javascript
function myErrorHandler(error) {
  checkoutInstance.unmount()
  switch (error.code) {
    case 'checkout-expired':
      // In this case a new Checkout session must be created
      const manifest = await yourFunctionToGetTheManifest()
      checkoutInstance = startCheckout(manifest, {
        onError: myErrorHandler
      })
      break
    case 'already-paid':
      document.write('Your order was already paid. Thank you.')
      break
    case 'checkout-canceled':
      document.write('Your checkout was canceled.')
      break
    default:
      document.write('Unable to process payment, please try again.')
  }
}

const checkoutInstance = startCheckout(manifest, {
  onError: myErrorHandler
})
```

The list of possible errors is further documented in [the reference](/checkout/reference#sdk)

### How to use popup module

If you want to integrate Checkout and display it in a popup this is what you need to do:

1. Import easypay's Checkout SDK into you project:

   - See [Integrating in your page](https://github.com/Easypay/docs/blob/afigueiredo/CR-1526_Document_checkout_sdk_display_option/content/en/checkout/guide.md#integrating-in-your-page)

2. Add a button with an id to your page:

```html
<button id="checkout">Open Popup</button>
```

```js
function initEasypayCheckoutSDK(manifest) {
  checkoutInstance = easypayCheckout.startCheckout(manifest, {
    id: 'checkout',
    display: 'popup',
  })
}
```

Result:

<img width="746" alt="Checkout Popup Mode" src="https://user-images.githubusercontent.com/30448483/172881494-7265ff97-d142-4fee-9a2b-047b986dbefc.png">
