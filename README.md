# Rewardful REST API Documentation

_**Note:** the [Rewardful](https://www.getrewardful.com/) REST API is currently in private-beta. Please contact us at hello@getrewardful.com with any questions or suggestions!_

- [Introduction](#introduction)
  - [Authentication](#authentication)
  - [Request and Response Formats](#request-response-formats)
  - [Errors](#errors)
- [Affiliates](#affiliates)
  - [Create Affiliate](#create-affiliate)
  - [Show Affiliate](#show-affiliate)
  - [Update Affiliate](#update-affiliate)
- [Webhooks](#webhooks)
  - [Endpoints](#webhook-endpoints)
  - [Requests](#webhook-requests)
  - [Responses, Errors, and Retries](#webhook-responses)
  - [Event Types](#webhook-event-types)
  - [Signed Webhooks](#signed-webhooks)

---

<a id="introduction"></a>
# Introduction

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

<a id="affiliates"></a>
# Affiliates

The create, show, and update endpoints all return a JSON representation of the affiliate in the format below.

Additional data for reporting (i.e. referral and reward details) will be added as we move forward.

```json
{
  "id": "d0ed8392-8880-4f39-8715-60230f9eceab",
  "created_at": "2019-05-09T16:18:59.920Z",
  "updated_at": "2019-05-09T16:25:42.614Z",
  "first_name": "Adam",
  "last_name": "Jones",
  "email": "adam.jones@example.com",
  "paypal_email": null,
  "stripe_customer_id": "cus_ABCDEF123456",
  "stripe_account_id": "acct_ABCDEF123456",
  "visitors": 100,
  "leads": 42,
  "conversions": 18,
  "campaign": {
    "id": "a638ebe4-291d-47cd-a1dc-1519f9331bbd",
    "created_at": "2019-04-27T18:13:13.123Z",
    "updated_at": "2019-05-05T20:58:24.200Z",
    "name": "Best Friends of Kyle"
  },
  "links": [
    {
      "id": "eb844960-6c42-4a3b-8009-f588a42d8506",
      "url": "http://www.example.com/?via=adam",
      "token": "adam",
      "visitors": 100,
      "leads": 42,
      "conversions": 18,
    }
  ]
}
```

<a id="create-affiliate"></a>
## Create Affiliate

This endpoint allows merchants to create affiliates on demand.

Both normal affiliates and "customer referrers" can be created through this endpoint. To create a customer referrer, simply pass the `stripe_customer_id` parameter that indicates the Stripe Customer that should receive account credits as rewards.

### Request

| Method  | URL |
| --- | --- |
| `POST`  | https://api.getrewardful.com/v1/affiliates  |

### Parameters

| Parameter | Required? | Description |
| --- | --- | --- |
| `first_name` | Yes | The affiliate's first name. |
| `last_name` | Yes | The affiliate's last name. |
| `email` | Yes | The affiliate's email address. |
| `stripe_customer_id` | No | For customer referral programs, this is the Stripe Customer that will receive account credits as rewards. |
| `token` | No | Alphanumeric code to be used for links, ex: ``?via=token` Must contain only letters, numbers, and dashes. |
| `campaign_id` | No | The UUID of the campaign this affiliate should be added to. Affiliate will be added to your default campaign if this parameter is blank. |
| `receive_new_commission_notifications` | No | Whether or not the affiliate should receive emails when new rewards and commissions are earned. Accepts `true` (default) or `false`.

### Response

| | Code | Body |
| --- | --- | --- |
| **Success** | `200` | JSON object describing the affiliate. |
| **Invalid** | `422` | JSON object describing validation errors. |

### Example:

```bash
curl --request POST \
  --url https://api.getrewardful.com/v1/affiliates \
  -u YOUR_API_SECRET: \
  -d first_name=James \
  -d last_name=Bond \
  -d email=jb007@mi6.co.uk \
  -d token=jb007 \
  -d stripe_customer_id=cus_ABC123
```

<a id="show-affiliate"></a>
## Show Affiliate

This endpoint will return some basic details about the affiliate and their unique tracking URL, including the number of visitors, leads, and conversions for that affiliate.

### Request

| Method  | URL |
| --- | --- |
| `GET`  | https://api.getrewardful.com/v1/affiliates/<:id>  |

### Response

| | Code | Body |
| --- | --- | --- |
| **Success** | `200` | JSON object describing the affiliate. |
| **Not Found** | `404` | JSON object describing the error. |

### Example:

```bash
curl https://api.getrewardful.com/v1/affiliates/d0ed8392-8880-4f39-8715-60230f9eceab \
  -u YOUR_API_SECRET:
```

<a id="update-affiliate"></a>
## Update Affiliate

This endpoint allows merchants to update the affiliate’s name and email.

Future functionality:

- Allow an active flag to be passed that disables/enables the affiliate account. This will allow merchants to deactivate the corresponding Rewardful affiliate account when a customer cancels their subscription (so that cancelled customers no longer earn rewards).
- We may eventually automatically sync name and email changes from Stripe. If the Stripe customer’s email changes, Rewardful will receive a webhook notification and update the affiliate to match the new data from Stripe.

### Request

| Method  | URL |
| --- | --- |
| `PUT`  | https://api.getrewardful.com/v1/affiliates/<:id>  |

### Parameters

| Parameter | Required? | Description |
| --- | --- | --- |
| `first_name` | No | The affiliate's first name. |
| `last_name` | No | The affiliate's last name. |
| `email` | No | The affiliate's email address. |
| `stripe_customer_id` | No | For customer referral programs, this is the Stripe Customer that will receive account credits as rewards. |
| `campaign_id` | No | The UUID of the campaign this affiliate should be moved to. [Learn more about moving affiliates between campaigns.](https://help.getrewardful.com/en/articles/2330644-working-with-multiple-campaigns#moving-affiliates-between-campaigns) |
| `receive_new_commission_notifications` | No | Whether or not the affiliate should receive emails when new rewards and commissions are earned. Accepts `true` or `false`.

### Response

| | Code | Body |
| --- | --- | --- |
| **Success** | `200` | JSON object describing the affiliate. |
| **Not Found** | `404` | JSON object describing the error. |
| **Invalid** | `422` | JSON object describing validation errors. |

### Example

```bash
curl --request PUT \
  --url https://api.getrewardful.com/v1/affiliates/d0ed8392-8880-4f39-8715-60230f9eceab \
  -u YOUR_API_SECRET: \
  -d first_name=Jamie \
  -d email=james.bond@mi6.co.uk
```

<a id="webhooks"></a>
# Webhooks

Rewardful can send webhook events that notify your application any time an event happens in your account. A common example is to receive a webhook when an affiliate signs up, which allows you to subscribe that affiliate to an email campaign in an external email service provider.

<a id="webhook-endpoints"></a>
## Endpoints

To receive webhook events, you must add a _webhook endpoint_ to your Rewardful account. You can request an endpoint be added by emailing hello@getrewardful.com.

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

During the private beta, we're continually rolling out more event types. If there's a specific event you're interested in, let us know by emailing us at hello@getrewardful.com.

| Event | Description |
| --- | --- |
| `affiliate.created` | Occurs when an affiliate signs up for your program, or is created through the Rewardful dashboard or API. |
| `affiliate.confirmed` | Occurs when an affiliate successfully confirms their email address. |
| `affiliate.updated` | Occurs when an affiliate's details are updated. |

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
$postBody = file_get_contents("php://input");
$expectedSignature = hash_hmac('sha256', $postBody, 'my-rewardful-signing-secret')

if ($expectedSignature == $_SERVER['X-Rewardful-Signature']) {
  // The request is legitimate and can be safely processed.
}

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
