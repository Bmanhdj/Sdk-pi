# Javascript SDK

The JS SDK is the frontend SDK, designed to be used in your HTML pages or Single-Page Apps, served in the Pi Browser.

In order to enable the SDK to function correctly, you need to declare your apps on the Developer Portal (open
develop.pi in the Pi Browser to access the Developer Portal).

This SDK is **not** for a server-side NodeJS app.


## Installation

Add the following `script` tag to all pages where you need the SDK to be available:

```html
<script src="https://sdk.minepi.com/pi-sdk.js"></script>
```

This will load the Pi Network JS SDK as a global `window.Pi` object.

## Usage

### Authenticate a user

You cannot perform any user-related operations (e.g read the user's info, request a payment from them) until you
have successfully authenticated the user. The first time, they will be presented with a dialog asking for
their consent to share their data with your app.

```javascript
// Identify the user with their username / unique network-wide ID, and get permission to request payments from them.
const scopes = ['username', 'payments'];
function onOpenPaymentFound(payment) { /* ... */ }; // Read more about this in the SDK reference

Pi.authenticate(scopes, onOpenPaymentFound).then(function(auth) {
  console.log(`Hello ${auth.user.username}. Your unique ID is ${auth.user.pi_id}`);
}).catch(function(error) {
  console.error(error);
});
```

### Request a payment

The `createPayment` method enables you to request a payment from the current user to your app's account.

The user will be prompted with a modal provided by the Pi Wallet, enabling them to sign the
transaction and submit it to the Pi blockchain.

```javascript

const payment = Pi.createPayment({
  amount: 3.14, // Amount of π to be paid
  reason: "Please pay for your order #1234", // User-facing explanation of the payment
  metadata: { orderId: 1234, itemIds: [11, 42, 314] }, // Developer-facing metadata
}, { // Read more about those callbacks in the details docs linked below.
  onPaymentIdReceived: onPaymentIdReceived,
  onTransactionSubmitted: onTransactionSubmitted,
  onPaymentCancelled: onPaymentCancelled,
  onPaymentError: onPaymentError,
});

```

This code block is a **simplified example** to give you a sense of how it works.

In order to make sure that all involved parties (your app, your server, the Pi servers, and the Pi blockchain) are in sync,
the payment needs to go through a **Server-Side Approval** flow and a **Server-Side Completion** flow.

Please refer to:
* [the full Payments documentation](./payments.md) to learn about the complete payment flow
* [the Platform API documentation](./platform_API.md) to learn how to confirm the payment and acknowledge it from your
  server
* the Demo App (coming soon!) to view an example of how you can implement the various required flows in your app's code.
