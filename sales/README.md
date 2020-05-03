# Sales

A sale represents a purchase made by a referred customer. It may be a one-time charge or a subscription renewal fee.

Sales represent _all_ purchases made by a referral, not just those sales that result in a commission for the affiliate.

<a id="object"></a>
## The sale object

### Example

```json
{
  "id": "1566a74e-e9ad-4849-b024-e1be9ef316ff",
  "currency": "USD",
  "referral": {
    "id": "3aafa13f-0a41-43d2-8d3a-a14e8c6a4621",
    "link": {
      "id": "32a19d65-2b68-434d-a401-e72ca7f24d81",
      "url": "http://www.example.com/?via=jb007",
      "leads": 7,
      "token": "jb007",
      "visitors": 8,
      "conversions": 6
    },
    "visits": 14,
    "customer": {
      "id": "cus_ABC123",
      "name": "Fred Durst",
      "email": "fred@example.com",
      "platform": "stripe"
    },
    "created_at": "2020-05-02T21:27:13.131Z",
    "expires_at": "2020-07-01T21:27:13.131Z",
    "updated_at": "2020-05-02T21:40:33.106Z",
    "deactivated_at": null,
    "conversion_state": "conversion",
    "stripe_account_id": "acct_ABC123",
    "stripe_customer_id": "cus_ABC123"
  },
  "affiliate": {
    "id": "dc939584-a94a-4bdf-b8f4-8d255aae729c",
    "email": "james@mi6.co.uk",
    "leads": 7,
    "campaign": {
      "id": "ae37c8ce-f82b-4e1b-9653-b802ae459a62",
      "name": "Friends of MI6",
      "created_at": "2020-04-27T00:24:08.199Z",
      "updated_at": "2020-04-27T00:24:08.199Z"
    },
    "visitors": 153,
    "last_name": "Bond",
    "created_at": "2020-04-27T00:24:08.334Z",
    "first_name": "James",
    "updated_at": "2020-05-02T21:40:33.115Z",
    "conversions": 6,
    "confirmed_at": "2020-04-27T00:24:08.331Z",
    "paypal_email": null,
    "sign_in_count": 0,
    "stripe_account_id": null,
    "unconfirmed_email": null,
    "stripe_customer_id": null,
    "paypal_email_confirmed_at": null,
    "receive_new_commission_notifications": true
  },
  "charged_at": "2020-05-02T21:40:28.000Z",
  "created_at": "2020-05-02T21:40:35.287Z",
  "updated_at": "2020-05-02T21:40:35.287Z",
  "invoiced_at": "2020-05-02T21:40:27.000Z",
  "stripe_charge_id": "ch_ABC123",
  "tax_amount_cents": 125,
  "sale_amount_cents": 2500,
  "stripe_account_id": "acct_ABC123",
  "charge_amount_cents": 2625,
  "refund_amount_cents": 0
}
```
