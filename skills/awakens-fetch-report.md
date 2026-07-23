---
name: Fetch a genetic trait report from Genomelink
description: >-
  Complete the OAuth 2.0 authorization-code flow to obtain a user's access
  token, then fetch a genetic trait report (by trait name and population) from
  the Genomelink Developer API.
api: Genomelink Developer API
base_url: https://genomelink.io
auth: oauth2 authorization_code (Bearer access token)
operations:
- GET /v1/reports/{name}/?population={population}
- POST /v1/enterprise/reports/
source: https://github.com/genomelink/genomelink-python
---

# Fetch a genetic trait report from Genomelink

Use this skill to read a consenting user's genetic trait report through the
Genomelink Developer API. All access is user-consented via OAuth 2.0.

> Availability note: AWAKENS currently marks the public Developer API as "not
> available" on the developers page. Exercise the flow against the documented
> test token first.

## 1. Send the user to authorize
Build the authorization URL and redirect the user:

```
https://genomelink.io/oauth/authorize?response_type=code&client_id=<CLIENT_ID>&redirect_uri=<CALLBACK_URL>&scope=<SCOPE>
```

The user signs in at genomelink.io and consents to sharing the requested
report(s). Genomelink redirects back to your `redirect_uri` with a `?code=`.

## 2. Exchange the code for an access token
POST to the token endpoint with the authorization code:

```
POST https://genomelink.io/oauth/token
Content-Type: application/x-www-form-urlencoded

grant_type=authorization_code&code=<CODE>&client_id=<CLIENT_ID>&client_secret=<CLIENT_SECRET>&redirect_uri=<CALLBACK_URL>
```

Handle the OAuth `error` codes (invalid_grant, invalid_client, access_denied,
...) documented in `errors/awakens-oauth-errors.yml`.

## 3. Fetch the report
Call the report endpoint with the Bearer access token, addressing the trait by
`name` and `population`:

```
GET https://genomelink.io/v1/reports/eye-color/?population=european
Authorization: Bearer <ACCESS_TOKEN>
```

The response carries `phenotype`, `population`, `scores`, and `summary`
(with `summary.text` giving the human-readable interpretation) — see
`data-model/awakens-data-model.yml`.

## Enterprise variant
For enterprise access, exchange the user token using your client secret:

```
POST https://genomelink.io/v1/enterprise/reports/
Authorization: Bearer <CLIENT_SECRET>
token=<USER_ACCESS_TOKEN>
```

## Testing
Use the documented magic test token to get a canned report without a live grant
(`sandbox/awakens-sandbox.yml`): `GENOMELINKTEST` / `GENOMELINKTEST001`.
