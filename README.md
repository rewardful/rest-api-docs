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
  "referrals": 0,
  "conversions": 0,
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
      "referrals": 0,
      "conversions": 0
    }
  ]
}
```

<a id="create-affiliate"></a>
## Create Affiliate

This endpoint allows merchants to create affiliates on demand

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

This endpoint will return some basic details about the affiliate and their unique tracking URL, including the number of clicks and conversions for that affiliate.

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
- We may automatically sync name and email changes from Stripe. If the Stripe customer’s email changes, Rewardful will receive a webhook notification and update the affiliate to match the new data from Stripe.

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
