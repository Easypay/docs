---
title: Configuration of Necessary Data
description: ""
position: 22
category: First Steps
---

## Configuration for new APIs

TODO

For your convenience, during tests you can use the following ID/Key pair:

```
AccountId: 2b0f63e2-9fb5-4e52-aca0-b4bf0339bbe6
ApiKey: eae4aa59-8e5b-4ec2-887d-b02768481a92
```

These will **only work for testing purposes** and **won't work** in production code.

## CIN

TODO

### CCs (Credit cards?)

TODO

## Authentication

Server-to-server requests to easypay APIs must be authenticated via **Account ID** and **API Key**.
To do so, include the following HTTP headers in your API requests:

```
AccountId: <YOUR_ACCOUNT_ID>
ApiKey: <YOUR_API_KEY>
```

For instance:

```
AccountId: 2b0f63e2-9fb5-4e52-aca0-b4bf0339bbe6
ApiKey: eae4aa59-8e5b-4ec2-887d-b02768481a92
```

When these headers are missing or incorrect, a `403` HTTP status code is returned.