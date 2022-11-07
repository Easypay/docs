To test your implementation without actually transferring funds, you should:

- Create the Checkout session using the testing API endpoint: `api.test.easypay.pt`
- Set the `testing` property of the SDK to `true`

```javascript
easypayCheckout.startCheckout(manifest, {
  display: 'popup',
  testing: true,
})
```

Remember to remove the `testing` property when switching to the `api.prod.easypay.pt` endpoint, otherwise Checkout will be unable to find the created Checkout sessions.
