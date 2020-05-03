# Rewardful REST API Documentation

_**Note:** the [Rewardful](https://www.getrewardful.com/) REST API is currently in private-beta. Please contact us at hello@getrewardful.com with any questions or suggestions!_

- [REST API](#rest-api)
  - [Authentication](#authentication)
  - [Request and Response Formats](#request-response-formats)
  - [Errors](#errors)
  - [Resources](#resources)
- [Webhooks](#webhooks)
  - [Endpoints](#webhook-endpoints)
  - [Requests](#webhook-requests)
  - [Responses, Errors, and Retries](#webhook-responses)
  - [Event Types](#webhook-event-types)
    - [Affiliates](#webhook-event-affiliates)
    - [Referrals](#webhook-event-referrals)
    - [Sales](#webhook-event-sales)
    - [Commissions](#webhook-event-commissions)
  - [Signed Webhooks](#signed-webhooks)

---

<a id="rest-api"></a>
# REST API

<a id="authentication"></a>
## Authentication

All API requests require authentication with [HTTP Basic Auth](https://en.wikipedia.org/wiki/Basic_access_authentication), similar to how Stripe authenticates. Provide your API Secret as the basic auth `username` value. You do not need to provide a password.

```bash
curl https://api.getrewardful.com/v1/affiliates/7B016217-18AF-44DD-A30C-0DE0C1534D2A \
  -u YOUR_API_SECRET:
```

Your API Secret can be found on the [Company Settings](https://app.getrewardful.com/company/edit) page. **Keep your API Secret safe!** Do not commit it to your version control system or share it with third-parties.

<a id="request-response-formats"></a>
## Request and Response Formats

Rewardful will provide a JSON-based REST API through which merchants can create affiliate accounts and fetch data for reporting. Endpoints accept [form-encoded](https://en.wikipedia.org/wiki/POST_(HTTP)#Use_for_submitting_web_forms) request bodies and return [JSON-encoded](http://www.json.org/) responses.

Rewardful uses [UUID strings](https://en.wikipedia.org/wiki/Universally_unique_identifier) for primary keys (IDs) for all resources. If you plan to store Rewardful IDs in your database, make sure to use a column type (string, UUID, etc) appropriate for your database engine.

Dates and times in the Rewardful API are represented as [ISO 8601](https://en.wikipedia.org/wiki/ISO_8601) formatted strings.

<a id="errors"></a>
## Errors

Missing or invalid authorization will return a `401 Unauthorized` JSON response:

```json
{  "error": "Invalid API Secret." }
```

Requesting a nonexistent object will return a `404 Not Found` JSON response:

```json
{  "error": "Affiliate not found: " }
```

Passing invalid data to a create/update endpoint will return a `422 Unprocessable` Entity JSON response:

```json
{
  "error": "Could not create affiliate.",
  "details": ["Email can't be blank"]
}
```

<a id="resources"></a>
## Resources

- [Affiliates](affiliates)

<a id="webhooks"></a>
# Webhooks

Rewardful can send webhook events that notify your application any time an event happens in your account. A common example is to receive a webhook when an affiliate signs up, which allows you to subscribe that affiliate to an email campaign in an external email service provider.

<a id="webhook-endpoints"></a>
## Endpoints

To receive webhook events, you must add a _webhook endpoint_ to your Rewardful account. You can add, edit, or remove endpoints from the [Webhooks](https://app.getrewardful.com/webhooks) page in your Rewardful dashboard.

And endpoint is simply a URL _on your application_ that Rewardful will send data to when an event occurs. Endpoints must be HTTP/SSL.

An example endpoint: `http://www.example.com/webhooks/rewardful`

<a id="webhook-requests"></a>
## Requests

Webhook data is sent as JSON in the POST request body to your configured endpoints.

The JSON payload has three root keys:

1. `object` represents the object that triggered the event (an affiliate, campaign, conversion, commission, etc.). The object's JSON structure will be identical to the structure returned by the REST API endpoints for that object type.
1. `event` contains metadata about the webhook event.
1. `request` contains metadata about this specific request to your endpoint.

### Example Payload

Webhook payloads contain these top-level items:

| Key | Description |
| --- | --- |
| `object` | A JSON representation of the record (Affiliate, Commission, etc) that triggered the event. |
| `event` | Metadata about the event itself. |
| `request` | Metadata about this specific request. |

Below is an example of the JSON that would be posted to your endpoint for the `affiliate.confirmed` event, i.e. when an affiliate has successfully confirmed their email address.

```json
{
  "object": {
    "id": "3433fffa-8255-4843-81b1-22aebc21eb34",
    "first_name": "James",
    "last_name": "Bond",
    "email": "james@mi6.co.uk",
    "created_at": "2019-06-24T00:43:36.097Z",
    "updated_at": "2019-06-27T18:38:41.684Z",
    "referrals": 42,
    "conversions": 3,
    "confirmed_at": "2019-06-24T00:43:36.095Z",
    "links": [{
      "id": "81bf1128-0ba1-4c1c-835c-286521f8791a",
      "url": "http://www.demo.com:8080/?via=james",
      "token": "james",
      "referrals": 42,
      "conversions": 3
    }],
    "campaign": {
      "id": "82f26208-778e-4e98-a467-6ee264cf96d5",
      "name": "Friends of Acme",
      "created_at": "2019-06-24T00:43:35.985Z",
      "updated_at": "2019-06-24T00:43:35.985Z"
    },
    "paypal_email": null,
    "sign_in_count": 0,
    "stripe_account_id": null,
    "unconfirmed_email": null,
    "stripe_customer_id": null,
    "paypal_email_confirmed_at": null,
    "receive_new_commission_notifications": true
  },
  "event": {
    "id": "7fcdc81d-996a-4b7b-8faf-1db98b9d5507",
    "type": "affiliate.confirmed",
    "created_at": "2019-07-11T05:25:43.940Z",
    "api_version": "v1"
  },
  "request": {
    "id": "66a94f49-2b36-403c-83b8-91677cb1f460"
  }
}
```

<a id="webhook-responses"></a>
## Responses, Errors, and Retries

When your endpoint receives and successfully processes a webhook event, your web server should return a `200` response code.

Any response code from your endpoint other than a `200` will be interpreted as a failure.

If a webhook fails, Rewardful will continue to attempt delivery over the next 3 days using exponential backoff. This means if the failures continue, we'll wait longer and longer between retries, before finally giving up after 3 days.

<a id="webhook-event-types"></a>
## Event Types

This is a list of all the types of events we currently send. We may add more at any time, so in developing and maintaining your code, you should not assume that only these types exist.

<a id="webhook-event-affiliates"></a>
### Affiliates

The `object` key for these events will describe an [`affiliate`](./affiliates/README.md) object.

| Event | Description |
| --- | --- |
| `affiliate.created` | Occurs when an affiliate signs up for your program, or is created through the Rewardful dashboard or API. |
| `affiliate.confirmed` | Occurs when an affiliate successfully confirms their email address. |
| `affiliate.updated` | Occurs when an affiliate's details are updated. |
| `affiliate.deleted` | Occurs when an affiliate is deleted. |

<a id="webhook-event-referrals"></a>
### Referrals

The `object` key for these events will describe a [`referral`](./referrals/README.md) object.

| Event | Description |
| --- | --- |
| `referral.created` | Occurs when a referral is created. |
| `referral.lead` | Occurs when a referral transitions to a "lead" state. |
| `referral.converted` | Occurs when a referral transitions to a "conversion" state (i.e. paid customer). |
| `referral.deleted` | Occurs when a referral is deleted. |

<a id="webhook-event-sales"></a>
### Sales

| Event | Description |
| --- | --- |
| `sale.created` | Occurs when a sale is created. |
| `sale.updated` | Occurs when a sale is updated. |
| `sale.refunded` | Occurs when a sale is refunded. |
| `sale.deleted` | Occurs when a sale is deleted. |

<a id="webhook-event-commissions"></a>
### Commissions

| Event | Description |
| --- | --- |
| `commission.created` | Occurs when a new commission is created. |
| `commission.updated` | Occurs when a commission is updated. |
| `commission.paid` | Occurs when a commission is paid. |
| `commission.deleted` | Occurs when a commission is deleted. |

<a id="signed-webhooks"></a>
## Signed Webhooks

Each webhook request sent by Rewardful includes a unique signature that can be used to verify the authenticity of the request. You can use this to confirm that the webhook request is legitimate and not an attacker attempting to spoof your endpoint.

Rewardful generates signatures using a hash-based message authentication code ([HMAC](https://en.wikipedia.org/wiki/HMAC)) with [SHA-256](https://en.wikipedia.org/wiki/SHA-2). The signature is generated by hashing your endpoint's _Signing Secret_ with the webhook _request body_. You can view your endpoint's Signing Secret from the [Webhooks](https://app.getrewardful.com/webhooks) page in your Rewardful dashboard.

The signature is contained in the HTTP header `X-Rewardful-Signature`. You can verify the signature by hashing your Signing Secret with the request body, then comparing the result with `X-Rewardful-Signature`. If they match, it means the request is legitimate.

Here are some examples of how you can verify the signature in a few frameworks and programming languages:

#### Ruby on Rails

```ruby
expected_signature = OpenSSL::HMAC.hexdigest('sha256', 'my-rewardful-signing-secret', request.raw_post)

if expected_signature == request.headers['X-Rewardful-Signature']
  # The request is legitimate and can be safely processed.
end
```

#### PHP

```php
<?php

$payload = @file_get_contents('php://input');

if (strlen($payload) == 0) {
  http_response_code(401);
  die("rejected");
}

$headers = getallheaders();

if (!array_key_exists("X-Rewardful-Signature", $headers)) {
  http_response_code(401);
  die("rejected");
}

$expectedSignature = hash_hmac('sha256', $payload, 'my-rewardful-signing-secret');

if($expectedSignature !== $headers["X-Rewardful-Signature"]) {
  http_response_code(401);
  die("rejected");
}

// The request is legitimate and can be safely processed.

?>
```

### Django

```python
import hmac
import hashlib

expected_signature = hmac.new(
    'my-rewardful-signing-secret',
    msg=request.body,
    digestmod=hashlib.sha256
).hexdigest()

if expected_signature == request.headers['X-Rewardful-Signature']:
  # The request is legitimate and can be safely processed.
```
