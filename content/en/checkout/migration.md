---
title: Migration
description: ""
position: 34
category: Checkout
---

## Migrating from previous versions

If you were using the [previous version of Checkout](https://api.prod.easypay.pt/docs#tag/Checkout), these are the steps you should follow to upgrade to the latest version:


### Script tag

Replace the previous Checkout script with the new one:

```html
<script src="https://cdn.easypay.pt/checkout/2.2.0/"></script>
```

> If you wish to further migrate to an NPM package, follow the guide to [install `@easypaypt/checkout-sdk`](/checkout/guide#npm-import).

### Button

Replace the `<div id="easypay-checkout"></div>` with a button and give it your preferred text:

```html
<button id="easypay-checkout">Pay with easypay</button>
```

### Checkout Setup

Replace the call to `EasypayCheckoutUI.setup` with the new `easypayCheckout.startCheckout`:

```javascript
easypayCheckout.startCheckout(manifest, {
  display: 'popup',
})
```

If you were using the `modal.onCancel`, `modal.onClose` or `modal.onComplete` options, add the desired success/close behaviour(s):

```javascript

let successfulPaymentInteraction = false

function mySuccessHandler() {
  successfulPaymentInteraction = true
  // Previous modal.onComplete behaviour
}

function myCloseHandler() {
  if (successfulPaymentInteraction) {
    // Previous modal.onClose behaviour
  } else {
    // Previous modal.onCancel behaviour
  }
}

easypayCheckout.startCheckout(manifest, {
  display: 'popup',
  onSuccess: mySuccessHandler,
  onClose: myCloseHandler,
})
```

### UI Configuration

If you used any of the following configuration options during Checkout creation:

- `language`
- `logo_url`
- `hide_details`

You should move them to the initialisation options of the `startCheckout` function:

```javascript
easypayCheckout.startCheckout(manifest, {
  display: 'popup',
  language: 'en', // or 'pt_PT'
  logoUrl: 'https://path.com/to/image.png',
  hideDetails: true, // or false
})
```

The remaining UI configuration options (CSS classes to apply to the elements) are not supported at the moment, as a richer styling API is being developed.

For more details and options, check the [Guide](/checkout/guide) and [Reference](/checkout/reference) pages.