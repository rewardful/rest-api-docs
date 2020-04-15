# Affiliates

- [The affiliate object](#object)
- [Create an affiliate](#create)
- [Retrieve an affiliate](#retrieve)
- [Update an affiliate](#update)

<a id="object"></a>
## The affiliate object

### Example

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

### Attributes

| Name  | Data Type | Description |
| --- | --- | --- |
| `id`  | `string`  | Unique identifier for the affiliate. |
| `created_at`  | `string`  | Time at which the affiliate was created. |
| `updated_at`  | `string`  | Time at which the affiliate was last updated. |
| `first_name`  | `string`  | The affiliate's first name. |
| `last_name`  | `string`  | The affiliate's last name. |
| `email`  | `string`  | The affiliate's email address name. |
| `paypal_email`  | `string` or `null`  | The affiliate's preferred PayPal email address, if different than `email`. |
| `visitors`  | `string`  | The total number of unique visitors attributes to this affiliate. |
| `leads`  | `string`  | The total number of leads (anonymous visitors who became customers) attributed to this affiliate. |
| `conversions`  | `string`  | The total number of conversions (leads who have made at least one payment) attributed to this affiliate. |
| `campaign`  | `string`  | A campaign object representing the campaign this affiliate belongs to. |
| `links`  | `array`  | An array of link objects representing the affiliate's links. |

#### Additional attributes for customer referrers

These attributes only apply to affiliates who are members of a customer referral program.

| Name  | Data Type | Description |
| --- | --- | --- |
| `stripe_customer_id`  | `string`  | Represents the Stripe Customer associated with this affiliate. This is the Stripe Customer that will receive rewards in the form of account credits. |
| `stripe_account_id`  | `string`  | The Stripe Account that contains the customer represented by `stripe_customer_id`. |

<a id="create"></a>
## Create an affiliate

This endpoint allows merchants to create affiliates on demand.

Both normal affiliates and customer referrers can be created through this endpoint. To create a customer referrer, simply pass the `stripe_customer_id` parameter that indicates the Stripe Customer that should receive account credits as rewards.

### Request

| Method  | URL |
| --- | --- |
| `POST`  | `https://api.getrewardful.com/v1/affiliates`  |

### Parameters

| Parameter | Required? | Description |
| --- | --- | --- |
| `first_name` | Yes | The affiliate's first name. |
| `last_name` | Yes | The affiliate's last name. |
| `email` | Yes | The affiliate's email address. |
| `stripe_customer_id` | No | For customer referral programs, this is the Stripe Customer that will receive account credits as rewards. |
| `token` | No | Alphanumeric code to be used for links, ex: `?via=token` Must contain only letters, numbers, and dashes. |
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

<a id="retrieve"></a>
## Retrieve an affiliate

Retrieves the details of an existing affiliate. You need only supply the unique affiliate identifier that was returned upon affiliate creation.

### Request

| Method  | URL |
| --- | --- |
| `GET`  | `https://api.getrewardful.com/v1/affiliates/:id`  |

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

<a id="update"></a>
## Update an affiliate

Updates the specified affiliate by setting the values of the parameters passed. Any parameters not provided will be left unchanged.

### Request

| Method  | URL |
| --- | --- |
| `PUT`  | `https://api.getrewardful.com/v1/affiliates/:id`  |

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
