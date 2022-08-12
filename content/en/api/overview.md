---
title: Overview
description: ""
position: 40
category: API
---

The easypay API is a collection of web services that allow you to receive payments from customers, be notified of those payments, refund them if necessary, among others.

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

As explained in [Configuration of Necessary Data](/first-steps/necessary-data#authentication), you must use your AccountId and ApiKey to authenticate requests to the API by setting the correct headers.

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
