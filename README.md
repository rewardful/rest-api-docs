# Rewardful REST API Documentation

_**Note:** the [Rewardful](https://www.getrewardful.com/) REST API is currently in private-beta. Please contact us at hello@getrewardful.com with any questions or suggestions!_

- [Introduction](#introduction)
  - [Authentication](#authentication)
  - [Request and Response Formats](#request-response-formats)
  - [Errors](#errors)

---

<a id="introduction"></a>
## Introduction

<a id="authentication"></a>
### Authentication

All API requests require authentication with [HTTP Basic Auth](https://en.wikipedia.org/wiki/Basic_access_authentication), similar to how Stripe authenticates. Provide your API Secret as the basic auth `username` value. You do not need to provide a password.

```shell
curl https://api.getrewardful.com/v1/affiliates/7B016217-18AF-44DD-A30C-0DE0C1534D2A \
  -u YOUR_API_SECRET:
```

Your API Secret can be found on the [Company Settings](https://app.getrewardful.com/company/edit) page. **Keep your API Secret safe!** Do not commit it to your version control system or share it with third-parties.

<a id="request-response-formats"></a>
### Request and Response Formats

Rewardful will provide a JSON-based REST API through which AND CO can create affiliate accounts and fetch data for reporting. Endpoints accept [form-encoded](https://en.wikipedia.org/wiki/POST_(HTTP)#Use_for_submitting_web_forms) request bodies and return [JSON-encoded](http://www.json.org/) responses.

Rewardful uses [UUID strings](https://en.wikipedia.org/wiki/Universally_unique_identifier) for primary keys (IDs) for all resources. If you plan to store Rewardful IDs in your database, make sure to use a column type (string, UUID, etc) appropriate for your database engine.

<a id="errors"></a>
### Errors

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
