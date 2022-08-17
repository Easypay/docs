---
title: Payment Methods
description: ""
position: 10
category: Payment Concepts
---

Payment methods are defined according to two different categories:
- **Pull / Push**: The merchant requests the funds from the customer, or the customer sends the funds directly to the merchant.
- **Synchronous / Asynchronous**: The charge happens immediately, or only after a period of time. 

## Pull or Push
Each method of payment is categorized as either **pull** or **push**, depending on how funds are transferred from the customer’s payment method.

- In a method with a **pull** behaviour, the customer needs only to accept the payment request from the merchant. Credit card payments and MB WAY are examples of the described behaviour. On the credit card, the customer accepts the payment by providing his credit card details, while on MB WAY they accept by interacting with the push notification on the smartphone. 

- In a method with a **push** behaviour, the customer takes the initiative of sending the funds to the merchant. Virtual IBAN or Multibanco are examples of such behaviour. On the Virtual IBAN, the customer should go to their bank account and create a SEPA transfer to the provided IBAN. On Multibanco, the customer should go to an ATM and create a payment to the entity and reference provided.

## Credit / Debit Card
A Credit / Debit card is a payment card issued to users (cardholders) to enable the cardholder to pay a merchant for a good or service. This is a **synchronous pull** payment method. This means that the payment happens immediately. Credit / Debit cards also support [authorizations](/concepts/authorisations-captures#authorisation) where you can hold an amount for later [capture](/concepts/authorisations-captures#capture). 

## MB WAY
MB WAY is a Portuguese local method. To use MB WAY, the customer must have a Portuguese Banking account as well as a smartphone Bank/MB WAY app installed. This is a **synchronous pull** payment method. The Merchant will issue a payment request that will trigger a push notification on the customer application. MB WAY also supports [authorizations](/concepts/authorisations-captures#authorisation) where you can hold an amount for later [capture](/concepts/authorisations-captures#capture).

## Multibanco
Multibanco is a Portuguese local method. This is an **asynchronous push** payment method. The Merchant creates a payment request that will generate the Multibanco order details. The details include: 
- An entity number
- A reference number
- The amount to pay
To pay, the customer introduces these details in an ATM or in their homebanking system.

This payment method has some ***edge cases*** that you should be aware of, such as the ones below:
- The customer can pay a different amount than the requested;
- Multibanco refunds are not currently supported by the vendor. If refunds are needed, you can offer them via SEPA Transfer.

## Direct Debit
Direct Debit is an **asynchronous pull** payment method in which a merchant withdraws funds from the customer's bank account. To do so, the customer must accept a SEPA Direct Debit (SDD) Mandate, which can be valid for multiple payments (therefore supporting single, frequent and subscription payments). To create the SDD Mandate, the merchant must supply the customer's name and IBAN.

Depending on the customer's bank, confirmations of Direct Debit payments may take up to 14 days.  
Direct Debit is not recommended for single payments due to the risk of fraudulent customers issuing chargebacks. 

## Virtual IBAN
Virtual IBAN is an **asynchronous push** payment method that follows the same principles of SEPA Bank Transfers: the merchant provides the customer with an IBAN and transfer amount that they should introduce in an ATM or homebanking system to specify the bank account to which funds are to be transferred.

In comparison with regular SEPA Transfers, Virtual IBAN has the benefit of protecting your bank account details by providing you with a virtual bank account number that supports single, frequent and subscription payments.

Although transfers to a single-use Virtual IBAN after the first payment are initially accepted by the customer's bank (and thus funds are temporarily withdrawn), they are automatically refunded by the IBAN issuer bank.

## Universo Flex
Universo Flex is a Portuguese local **synchronous pull** payment method similar to Credit Card, allowing the merchant to accept payments in 3 to 24 installments without having to be a credit intermediary.

## Santander Consumer Finance
Santander Consumer Finance is a Portuguese local **asynchronous pull** payment method where the customer applies for a loan to acquire the good or service in installments. The merchant does not need to be a credit intermediary.