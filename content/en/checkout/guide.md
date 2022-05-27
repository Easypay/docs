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

To begin a Checkout session, you are required to send a server-to-server `POST` request to the [`/checkout`](/checkout/reference#checkout) endpoint.

Like all API requests, this request is authenticated as described in [Authentication](/first-steps/necessary-data#authentication), which means you must include the `AccountId` and `ApiKey` headers.

An example payload is:

```json
{
  "type": [
    "single"
  ],
  "payment": {
    "methods": [
      "cc",
      "mb",
      "mbw",
      "dd",
      "vi",
      "uf",
      "sc"
    ],
    "type": "sale",
    "capture": {
      "descriptive": "Descriptive Example"
    },
    "currency": "EUR",
    "start_time": "2023-05-30 16:05",
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
  "config": null,
  "customer": null
}
```

You can check all the possible parameters in the [Checkout reference](/checkout/reference#checkout).

The service will return something like:

```json
{"id":"57cc19e9-f393-4cfa-9516-8807763b5096","session":"8zoaBOC0Mj5Mg_YAbdRCqYGI66Zc2DttAWeyVcedzhuT3r2IE8aS7l7H-sQOyppgawIv-wO48H_oKDHMzvMf1TeIJwo3Xu7HO7Tmj7y1iblt8t9JlEzyNdf5HurTvCeszC8PdRS4mbaPBCMCQ7g4xkG0qUuOEhiee5OB49MJJQ-Mo9vEnC_R4hzRBi-HSIMviTNDcCm5NiMnn__nWZxpfAmh6pYUg_eznqTGNbheYLu7ku_mBn7HvAdJQcgsmk6eeD-Z9S8SsK-ONHpbJQz1LNZsUkIIiWpNxPMrzD5Je0r2veawzAaIQmCeN8hYncHDfl5YZ7PP28MFVtEjMGOxsOsqxL4wmDOLPO5s6aWMq7Dxns-OGgf0r7dmaka4Y2mXovLVtNxsGgsNbBav8K86f5DceCDmPKBzYh-X5i1nx07LuqYKoJmA8NfyVP5tb-R3Y9dtwbPzcHc_AyQVKlsM0HtW3JXAdqoIBfsDgUqwASeuqQHEyIITKlcRvDIzWFaxTgXmmJauj6et7mo1GEM4L1gDkA1IXOd3n4HU2GvfPw==","config":null}
```

This return object is referred to as a **Checkout manifest**, and will be needed in the steps below.


## Integrating in your page
### Script tag

Include the easypay Checkout SDK in your page:

```html
<!-- TODO: include from CDN -->
<script src="TODO_CORRECT_LINK/0.0.1/easypay-checkout-sdk.js"></script>
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
easypayCheckout.startCheckout(manifest);
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
  id: 'checkout-form'
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
The SDK includes a callback parameter that you can pass to get notified when the user ends the interaction:

```javascript
const checkoutInstance = startCheckout(manifest, {
  onMessage: myMessageHandler
})
```

In your message handler, you should receive a `string` parameter that will indicate which event has happened (currently only `complete` is available).

```javascript
function myMessageHandler(checkoutMessage) {
  if (checkoutMessage === 'complete') {
    checkoutInstance.unmount()
    document.write('Checkout session complete. Thank you.')
  }
}
```

