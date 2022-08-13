---
title: Authorisations and Captures
description: ""
position: 12
category: Payment Concepts
---

Certain payment methods like credit card and MB WAY go through two distinct phases: *authorisation* and *capture*. Our integrations allow you to move through them separately (issuing a card authorisation first and later capturing the funds) or in a single step referred to in our APIs as `sale` (authorisation followed by capture).

## Authorisation

Authorisation is the first step of the process and involves guaranteeing that the buyer has sufficient funds or credit available to make the payment. If so, the funds are put on hold: the customer cannot use them to pay for other goods or services, but they're not yet transferred to the merchant's account. If the merchant fails to capture the funds in the following days, the hold is lifted.

Authorisations are useful to make sure customers have the means to purchase your product/service before actually charging them. You can issue the authorisation at the moment an order is placed and only actually transfer the funds when you ship the items or provide the service. Another interesting use case of authorisations is [multi-captures](/concepts/authorisations-captures#multi-captures).

## Capture

A capture is the actual transfer of funds from the buyer to the merchant.

## Sale

In many of our integrations, specifying `sale` as the type of operation will automatically perform an authorisation and a capture, immediatelly transferring the funds from the customer to the merchant in a single step (if successful).

## Multi-captures

It is also possible to issue an authorisation for a value that will later be split among different captures. One example is a store supporting split payments for orders of multiple vendors. The full amount of the order is authorised but the different captures can be issued at different times, after each vendor confirms or ships the goods.
