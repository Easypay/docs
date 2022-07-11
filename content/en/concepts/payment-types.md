---
title: Payment Types
description: ""
position: 11
category: Payment Concepts
---

## Single

Single payments are one-time transactions that will let your customers pay an amount of money to you. Examples may include a purchase of a good or service, or a donation. Every payment method supported by easypay allows single payments.

You can accept single payments entirely through our [Checkout](/checkout/overview) solution or by calling our [Payments API](/api/payments).

## Frequent

Frequent payments are repeatable transactions of varying sums without the client having to enter their payment details again. The original details can be obtained (*tokenized*) through our [Checkout](/checkout/overview) solution, but each payment will have to be charged via the [Payments API](/api/payments).

It is possible to limit the transferred sums by choosing minimum or maximum values, either to the total sum of the transactions or each individual transaction.

Supported methods for frequent payments are: Credit Card, MB WAY, Multibanco, and Direct Debit.

## Subscription

Subscriptions are periodic payments of the same amount of money, configured solely through our [Payments API](/api/payments). Checkout support will be coming soon.

Subscriptions support a number of different frequencies, ranging from daily to once every three years. Their last payment can be determined by the end date of the subscription, as well as the total number of payments captured. They also support a configurable number of retries for each capture in case of failure, as well as falling back to another (single) payment method if the recurring one is not successful.

Supported methods for subscriptions are: Credit Card and Direct Debit.