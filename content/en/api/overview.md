---
title: Overview
description: ""
position: 40
category: API
---

The easypay API is a collection of web services that allow you to receive payments from customers, be notified of those payments, refund them if necessary, among others.

A more thorough reference can be found in https://api.prod.easypay.pt/docs.
## URLs

The API endpoints are available through the following domains:

- For test environment:
  ```bash
    https://api.test.easypay.pt/
  ```
- For production environment:
  ```bash
    https://api.prod.easypay.pt/
  ```

## Authentication

To create credentials for your account you can follow [this guide](https://www.easypay.pt/en/como-gerar-chaves-de-autenticacao-2/). 

Server-to-server requests to easypay APIs must be authenticated via **Account ID** and **API Key**.
To do so, include the following HTTP headers in your API requests:

```
AccountId: <YOUR_ACCOUNT_ID>
ApiKey: <YOUR_API_KEY>
```

When these headers are missing or incorrect, a `403` HTTP status code is returned.

For your convenience, during tests you can use the following ID/Key pair:

```
AccountId: 2b0f63e2-9fb5-4e52-aca0-b4bf0339bbe6
ApiKey: eae4aa59-8e5b-4ec2-887d-b02768481a92
```

These will **only work for testing purposes** and **won't work** in production.

## Errors

Easypay uses HTTP response status codes to indicate the success or failure of your API requests. If your request fails, Easypay returns an error using the appropriate status code.

In general, there are three status code ranges you can expect:

- `2xx` success status codes confirm that your request worked as expected
- `4xx` error status codes indicate an error because of the information provided (for example, a required parameter was omitted).
- `5xx` error status codes are rare and indicate an error with Easypay’s servers.

Some `4xx` errors that could be handled programmatically (for example, a card is declined) include an error code (a short string with a brief explanation) as a value for `code`.

Below is a list of possible error codes that can be returned, along with additional information about how to resolve them. For your convenience, these types of errors also include the `doc_url` attribute with a direct link to the specific error code it corresponds to.

### Authentication

This error can have multiple potential causes. Here are some examples:

- Missing/Invalid AccountId and/or ApiKey Headers
- Your account has been blocked
- You don't enough permissions to the action requested

### Invalid Content Type

This error can only have one cause, you provided an invalid/unsupported content type header.

### Invalid JSON

This error happens when you provide a bad formated JSON payload.

### Invalid Params

This error indicates the information provided was incorrect of insufficient (for example, a required parameter was omitted).

### Internal

This error indicates that an unexpected error ocurred on our servers.
