---
title: Payment Methods
description: ""
position: 10
category: Payment Concepts
---

Each payment method is defined by 2 key characteristics:
- **Is it pull or push**: How the funds for the customer are acquired from the customer.
- **Synchronous / Asynchronous**: The charge happens immediately, or only after a delay. 

## Pull or Push
Each method of payment is categorized as either pull or push, depending on how funds are transferred from the customer’s payment method.

- Using a pull method, you debit the funds from the customer’s account after the customer has provided consent. Card payments are an example of a pull method: your customer’s card is debited when a payment is made, and no customer interaction is required for subsequent debits.

- Using a push method, the customer sends the funds to you. Digital  IBAN and Multibanco are good examples.

## Credit / Debit Card
A Credit / Debit card is a payment card issued to users (cardholders) to enable the cardholder to pay a merchant good or service. This is a Synchronous pull payment method. This means that the payment happens immediately. Credit / Debit cards also support Authorisations where you can captivate an amount for later capture. 

## MBWay
MBWay is a Portuguese local method. For using MBway the customer must have a Portuguese Banking account and also the MBWay app installed. This is an Asynchronous pull payment method. The Merchant will issue a payment request that will trigger a push notification on the customer MBWay application. MBWay also support Authorisations where you can captivate an amount for later capture.

## Multibanco
Multibanco is a Portuguese local method. This is an Asynchronous push payment method. The Merchant creates a payment request that will create a Multibanco voucher. The Multibanco voucher will include: 
- A payment reference
- An entity number
- The payable amount
This payment method has some ***edge cases*** that you should be aware of, like the ones below:
- The Customer can pay a different amount than the requested
- There is no way to refund a Multibanco payment, if that is needed you can only do it via SEPA Transfer.

## Direct Debit

## Digital IBAN
Digital IBAN is like a regular SEPA(Single Euro Payments Area) Bank Transfer but with some advantages like:
- Unambiguously identify the payment: Who is paying and what
- Transfers with an unique identifier

This is an Asynchronous push payment method. The Merchant creates a payment request that will create an IBAN for that payment intent. This payment method has some ***edge cases*** that you should be aware of, like the ones below:
- The Customer can pay a different amount than the requested
- We cannot guarantee that payment expires at some specific point in time

## Boleto Bancário

## Universo Flex

## Santander Consumer