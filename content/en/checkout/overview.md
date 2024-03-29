---
title: Overview
description: ""
position: 30
category: Checkout
---

Checkout is easypay's integrated solution for collecting payments in any website.

It requires no extensive programming knowledge by including a pre-built payment form directly in your website. Checkout is responsible for:
- Collecting user information.
- Allowing payment method selection.
- Collecting payment information.
- Invoking payment APIs.
- Displaying payment feedback.

![Checkout](/checkout/overview.png)

## Supported payment methods

Currently, the supported payment methods are:
- [Credit Card](/concepts/payment-methods#credit--debit-card)
- [MB WAY](/concepts/payment-methods#mb-way)
- [Multibanco Reference](/concepts/payment-methods#multibanco)
- [Direct Debit](/concepts/payment-methods#direct-debit)
- [Virtual IBAN](/concepts/payment-methods#virtual-iban)
- [Universo Flex](/concepts/payment-methods#universo-flex)
- [Santander Consumer](/concepts/payment-methods#santander-consumer)

To know how to choose from the available methods and integrate them with Checkout, read the [integration guide](/checkout/guide).

## Supported payment types

Both [single](/concepts/payment-types#single) and [frequent](/concepts/payment-types#frequent) payments, as well as [subscriptions](/concepts/payment-types#subscription) are accepted by Checkout.

## Integration methods

All Checkout interactions begin by making a server-to-server [request to create a Checkout session](/checkout/guide#creating-a-checkout-session). The request will return a *Checkout **manifest***, which is then used to configure and display a Checkout form created exclusively for that payment interaction.

You can then display a Checkout form in your page by invoking our [Checkout SDK](/checkout/guide#integrating-in-your-page). The SDK can be included from a CDN, via HTML `<script>` tag, or you can use it as an NPM dependency in your project and import it in JavaScript/TypeScript.

This two-step approach makes Checkout the easiest way to integrate easypay payments in your custom website.


