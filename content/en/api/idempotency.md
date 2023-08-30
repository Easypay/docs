---
title: Idempotency
description: ""
position: 43
category: API
---

## Safe Retries in Easypay API: The Role and Implementation of Idempotency Keys

Easypay API incorporates idempotency that enables the **safe re-submission of requests, eliminating the risk of unintentionally duplicating operations**.

This helps to circumvent unintentional replication due to failures and retries.

For instance, in an event of a timeout error, you can securely resend the same API payment call several times, assured that the payment detail will be debited only once.

To execute an idempotent request, simply include an additional `Idempotency-Key` header within the request parameters.

```shell
curl -L -X POST 'https://api.test.easypay.pt/2.0/checkout' \ 
  -H 'AccountId: 2b0f63e2-9fb5-4e52-aca0-b4bf0339bbe6' \ 
  -H 'ApiKey: eae4aa59-8e5b-4ec2-887d-b02768481a92' \ 
  -H 'Idempotency-Key: 435e08a0-e5a9-4216-acb5-44d6b96de612' \ 
  -H 'Content-Type: application/json' \ 
  --data-raw '{ "type": [ "single" ] ... }'
```

You can check if a request is a replay by checking if the `Idempotency-Replay` Header is present in the response and if it has the value `"true"`.

## Generating Idempotency Keys
Although the technique of creating unique keys is your choice, **remember random string with sufficient entropy to prevent overlaps is advisable.**

Easypay recommends the use of UUID V4 or ULID as Idempotency Key.

The maximum length for idempotency key is **50 characters**.

## How it works
Two requests with the same Idempotency-Key but different AccountId headers will be treated as different requests.

Easypay manages idempotency by storing the initial status code and response body for the initial request with a specific idempotency key.

The Idempotency layer compares the request body of the incoming request with that of the original request.

**If they differ**, the system triggers an error to prevent inadvertent misuse.

Repeated requests bearing the same key yield identical outcomes. The only exception pertains to **transient errors**, which we have identified as the following:

* Too Many Requests (HTTP status code 429)
* Bad Gateway (HTTP status code 502)
* Service Unavailable (HTTP status code 503)

The previously mentioned error codes are safe to retry.

To help the client applications decide whether to retry calls, the API's responses include an `X-Easypay-Should-Retry` header with value `"true"` or `"false"`.
Note that this header does not take into account the number of tries already made, so clients should keep track and only send a limited number of equal requests (e.g. 3).

If and when the header is not present, clients can use the following strategy to determine what to do: 

1. If no response was received at all, resend the request.
2. If the response has one of the following status codes, resend the request:
   * Conflict (HTTP status code 409), may indicate the original request was still in transit
   * Too Many Requests (HTTP status code 429)
   * Bad Gateway (HTTP status code 502)
   * Service Unavailable (HTTP status code 503)
3. If the response has status code 500 (Internal Server Error), resend the request as long as it's **not** a `POST` request.
4. Otherwise, don't repeat the request and handle the error.

Idempotency Keys are automatically purged from the system once they've been in existence for 24 hours, and a fresh request is initiated if a key is reused after its original has been deleted.

Only results from API endpoints that have started execution are stored.

**If your request fails authentication, it will not be governed by idempotency rules.**

Idempotency keys are acceptable in all POST/PATCH requests.

It is unnecessary and should be avoided to supply idempotency keys in GET and DELETE requests, as these types of requests are inherently idempotent by nature.
