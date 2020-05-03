# Referrals

A referral represents a unique visit to your website that came via an affiliate link.

A referral has three distinct conversion states:

| State | Description |
| --- | --- |
| Visitor | This is the default state for a new referral. It represents an anonymous visitor on your site who has not converted to a lead or conversion. |
| Lead | A lead is a visitor who has provided their contact information or "converted" to a non-paying state, i.e. a free trial or freemium account. |
| Conversion | A conversion represents a paying customer who arrived via an affiliate link. A "lead" becomes a "conversion" after paying you any amount |

<a id="object"></a>
## The referral object

### Example

```json
{
  "id": "e523da29-6157-4aac-b4b5-05b3b7b14fb6",
  "link": {
    "id": "32a19d65-2b68-434d-a401-e72ca7f24d81",
    "url": "http://www.example.com/?via=jb007",
    "leads": 6,
    "token": "jb007",
    "visitors": 7,
    "conversions": 5
  },
  "visits": 42,
  "customer": {
    "id": "cus_ABC123",
    "name": "Fred Durst",
    "email": "fred@example.com",
    "platform": "stripe"
  },
  "affiliate": {
    "id": "dc939584-a94a-4bdf-b8f4-8d255aae729c",
    "email": "james.bond@mi6.co.uk",
    "leads": 6,
    "campaign": {
      "id": "ae37c8ce-f82b-4e1b-9653-b802ae459a62",
      "name": "Friends of MI6",
      "created_at": "2020-04-27T00:24:08.199Z",
      "updated_at": "2020-04-27T00:24:08.199Z"
    },
    "visitors": 7,
    "last_name": "Bond",
    "created_at": "2020-04-27T00:24:08.334Z",
    "first_name": "James",
    "updated_at": "2020-05-03T19:39:03.028Z",
    "conversions": 5,
    "confirmed_at": "2020-04-27T00:24:08.331Z",
    "paypal_email": null,
    "sign_in_count": 0,
    "stripe_account_id": null,
    "unconfirmed_email": null,
    "stripe_customer_id": null,
    "paypal_email_confirmed_at": null,
    "receive_new_commission_notifications": true
  },
  "created_at": "2020-04-27T00:34:28.448Z",
  "expires_at": "2020-06-26T00:34:28.448Z",
  "updated_at": "2020-05-03T19:37:42.490Z",
  "deactivated_at": null,
  "conversion_state": "conversion",
  "stripe_account_id": "acct_ABC123",
  "stripe_customer_id": "cus_ABC123"
}
```
