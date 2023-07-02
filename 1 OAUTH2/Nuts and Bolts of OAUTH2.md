- [denokvoauth](https://deno.land/x/deno_kv_oauth@v0.2.5)
- [oauth2](https://oauth.net/2/)
- [fusionauth master oauth2](https://fusionauth.io/articles/oauth/modern-guide-to-oauth)
- [udemy nuts and bolts oauth2](https://www.udemy.com/course/oauth-2-simplified/learn/lecture/23715950#overview)
- [udemy advanced oauth2](https://www.udemy.com/course/advanced-oauth-security/learn/lecture/33982456#overview)

- authorization and api servers
- auth server is hotel front desk checks id and gives key card
- api server checks key card to see if this door is on the list of doors it can open and the expiration date is not past
- purpose of oauth2 is for multiple apps to share a user db for single sign on
- openid connect adds user info like profile info to oauth

OAUTH2 flow:
1 user is sent to oauth2 server to login 
2 user is redirected back to the app to get tokens
(app never sees password)

- oauth issues access tokens to apps
- openid connect issues id tokens to apps, which are statements about the users
- access (authz) and identity (authn)

### Roles
- user (resource owner)
- device (user agent)
- app (oauth client)
- authorization server (user db - manages auth to api it's protecting)
- api (resource server)

#### Flow Overview
	- user logs into auth server aka user db and user db aka auth server returns access token to the app, similar to a session cookie with user info via hand-rolled auth using bcrypt and web session cookie apis
	- app uses access token (in lieu of session cookie) to make api request 
	- existence of non-expired session cookie is sufficient for hand-rolled, but oauth requires api server to validate access token with each request, introducing an extra security step

#### Flow Steps
1) app builds uri and redirects user to oauth server with PKCE hash, app client id, redirect url, and scope
2) user logs into oauth server and confirms access to app
3) oauth server redirects user with auth token back to app via redirect url sent in step 1
4) app sends request for access token with auth token PKCE hash client id in header
5) oauth verifies PKCE hash for client id and responds with access token

```env
CLIENT_ID=7X2UY3CZE1vp59qBivV1KtyC73kh57mc
ISSUER_URL=https://dev-bz-14ku3.us.auth0.com
AUTHORIZATION_ENDPOINT=https://dev-bz-14ku3.us.auth0.com/authorize
TOKEN_ENDPOINT=https://dev-bz-14ku3.us.auth0.com/oauth/token
CODE_VERIFIER=080b4871e65004ac77e366c19f9e278a493dec63d9ffb4b224df1d89
CODE_CHALLENGE=r5dVLGqUPHN8gLqizD5Tusv32km7Uv0ANnJE-fD4Q3c
```

1 authorization request
```bash
https://dev-bz-14ku3.us.auth0.com/authorize?
  response_type=code&
  client_id=7X2UY3CZE1vp59qBivV1KtyC73kh57mc&
  state=ABCDEFG&
  redirect_uri=https://example-app.com/redirect&
  code_challenge=r5dVLGqUPHN8gLqizD5Tusv32km7Uv0ANnJE-fD4Q3c&
  code_challenge_method=S256

```

2 auth code response
```bash
https://example-app.com/redirect?code=hpjvwzXua5C2_YtMnaRbd4BwptA0LoR5fZ9vSnA96apkV&state=ABCDEFG
```

3 access code request
	- post request form encoded post body
```bash
curl -X POST https://dev-bz-14ku3.us.auth0.com/oauth/token \
  -d grant_type=authorization_code \
  -d redirect_uri=https://example-app.com/redirect \
  -d client_id=7X2UY3CZE1vp59qBivV1KtyC73kh57mc \
  -d client_secret=Pei2_VynOaZmrhQy_I9ac0e3AyUmHTYfs1iEnJU7fnTc3DhC_TxzCPSYCXoStxmW \
  -d code_verifier=080b4871e65004ac77e366c19f9e278a493dec63d9ffb4b224df1d89 \
  -d code=hpjvwzXua5C2_YtMnaRbd4BwptA0LoR5fZ9vSnA96apkV
```

4 access code response
```bash
{"access_token":"eyJhbGciOiJSUzI1NiIsInR5cCI6IkpXVCIsImtpZCI6InFEQm1Dc0NmamlsZk9nT0pvNmJNaiJ9.eyJpc3MiOiJodHRwczovL2Rldi1iei0xNGt1My51cy5hdXRoMC5jb20vIiwic3ViIjoiZ29vZ2xlLW9hdXRoMnwxMTYwNDUyNTEzMzAyNDMxMTAyMzUiLCJhdWQiOiJodHRwczovL2FwaS5leGFtcGxlLmNvbSIsImlhdCI6MTY4NzcwMTE1NiwiZXhwIjoxNjg3Nzg3NTU2LCJhenAiOiI3WDJVWTNDWkUxdnA1OXFCaXZWMUt0eUM3M2toNTdtYyJ9.MhlVq49aQdH4wXUghlx_-aXwqVP5LTca5iPhwbTUeaDhkv41_77-kJrde4qSLnryczWNp7AevYZM60NEh88o3WGs1vsFhSv_iNSkSpielBizZC7rorw0uvbQDdZgHt81e7_ri0KoN0bpMwIPPR74mBFg8SzYn7gnXRSNaDV5OVTZfFT9lW-Rfxw2G6wXlyvO1-JE9xhrtLDBUWok9DRlr66YrMO3Sd82KeqWbbZL3YXGn-G49F7GNj6oZpf_E3FpEUMu0bTlKGgle2171kacyioKBw_c0D_119HF_ofPe7QP4DjshF5s9rAoUhbxQ3IT1cb0wYv8pMK_XKdOI1Ltww","expires_in":86400,"token_type":"Bearer"}   
```

- auth0 tenant is user db


### Mobile Apps
- mobile app specific browser keeps user in app but app can't see inside browser, safe for oauth request (i think it has something to do with shared system cookies)
	- iphone use SFSafariViewController
	- android use Chrome Custom Tabs


### Refresh Token
{
"token_type": "Bearer",
"access_token": "abcdefg",
"expires_in": 3600,
"scope": "photos",
"refresh_token": "zyxvuw"
}

- using refresh token is POST request to token endpoint (same endpoint used by first request using auth code)
- env
```env
CLIENT_ID=7X2UY3CZE1vp59qBivV1KtyC73kh57mc
CLIENT_SECRET=Pei2_VynOaZmrhQy_I9ac0e3AyUmHTYfs1iEnJU7fnTc3DhC_TxzCPSYCXoStxmW
ISSUER_URL=https://dev-bz-14ku3.us.auth0.com
AUTHORIZATION_ENDPOINT=https://dev-bz-14ku3.us.auth0.com/authorize
TOKEN_ENDPOINT=https://dev-bz-14ku3.us.auth0.com/oauth/token
CODE_VERIFIER=e586ea8d593f2c92988ad9c98a2464a2a201128fac3ff91aa8fe3b09
CODE_CHALLENGE=G8-X1xuBOpS6xL1Fk1e4Gh9UjCgqpNOMr-1wtiIUgCI
```

- auth token request
```bash
https://dev-bz-14ku3.us.auth0.com/authorize?
    response_type=code&
    client_id=7X2UY3CZE1vp59qBivV1KtyC73kh57mc&
    state=abcdefg&
    scope=offline_access&
    redirect_uri=https://example-app.com/redirect&
    code_challenge=G8-X1xuBOpS6xL1Fk1e4Gh9UjCgqpNOMr-1wtiIUgCI&
    code_challenge_method=S256
  
```

- auth token response
```bash
`code=KgUip7Oug1HfYk-s2Eo_WZ7PzOwLYrxnl8w9WMA2NQ36t`

`state=abcdefg`
```

- access token request (to get refresh token)
```bash
curl -X POST https://dev-bz-14ku3.us.auth0.com/oauth/token \
  -d grant_type=authorization_code \
  -d redirect_uri=https://example-app.com/redirect \
  -d client_id=7X2UY3CZE1vp59qBivV1KtyC73kh57mc \
  -d client_secret=Pei2_VynOaZmrhQy_I9ac0e3AyUmHTYfs1iEnJU7fnTc3DhC_TxzCPSYCXoStxmW \
  -d code_verifier=e586ea8d593f2c92988ad9c98a2464a2a201128fac3ff91aa8fe3b09 \
  -d code=KgUip7Oug1HfYk-s2Eo_WZ7PzOwLYrxnl8w9WMA2NQ36t
```

```bash
{"access_token":"eyJhbGciOiJSUzI1NiIsInR5cCI6IkpXVCIsImtpZCI6InFEQm1Dc0NmamlsZk9nT0pvNmJNaiJ9.eyJpc3MiOiJodHRwczovL2Rldi1iei0xNGt1My51cy5hdXRoMC5jb20vIiwic3ViIjoiZ29vZ2xlLW9hdXRoMnwxMTYwNDUyNTEzMzAyNDMxMTAyMzUiLCJhdWQiOiJodHRwczovL2FwaS5leGFtcGxlLmNvbSIsImlhdCI6MTY4Nzc5NjMzMiwiZXhwIjoxNjg3ODgyNzMyLCJhenAiOiI3WDJVWTNDWkUxdnA1OXFCaXZWMUt0eUM3M2toNTdtYyIsInNjb3BlIjoib2ZmbGluZV9hY2Nlc3MifQ.BdkSb2BQIv8Bh36mUiVl4vLFriFclm69tyFCV0DFgVbViQbG-YsFDjHtw5YzAr3IxFQNjrYZH4rdY6KCvuc7EQZPeXD0X51sGpV0pGRYkojSD6lFccXRGJe1IAX7_qbCwolRTNAQvQrrZXRLwjrTN9mZIwvjYxRfhIiHLqMUsvq6OPX4KKhx7Bh5BHKyMcdGBwxrrqXUWZWulicz3j5FAVIPkg1eBsSmLTuRUJRmc2wr_wQ-pBIlTocbO7OqeggdfdUVIyfxqB8nUVDv7H1Mq46qe7QvFSkzbTE_n0eiAjd4je7wMNPw7OAqd20eqNALkqxEOqkuq2GNlfEc9HXn2Q","refresh_token":"PLO_kIG5L0h5nSN9t_JTKJDihT_Gk4PQkUWYKTr1yiuFQ","scope":"offline_access","expires_in":86400,"token_type":"Bearer"}
```

- access token request using refresh token
```bash

curl -X POST "https://dev-bz-14ku3.us.auth0.com/oauth/token" \
  -d "grant_type=refresh_token" \
  -d "client_id=7X2UY3CZE1vp59qBivV1KtyC73kh57mc" \
  -d "client_secret=Pei2_VynOaZmrhQy_I9ac0e3AyUmHTYfs1iEnJU7fnTc3DhC_TxzCPSYCXoStxmW" \
  -d "refresh_token=PLO_kIG5L0h5nSN9t_JTKJDihT_Gk4PQkUWYKTr1yiuFQ"

```

- access token response
```bash
{"access_token":"eyJhbGciOiJSUzI1NiIsInR5cCI6IkpXVCIsImtpZCI6InFEQm1Dc0NmamlsZk9nT0pvNmJNaiJ9.eyJpc3MiOiJodHRwczovL2Rldi1iei0xNGt1My51cy5hdXRoMC5jb20vIiwic3ViIjoiZ29vZ2xlLW9hdXRoMnwxMTYwNDUyNTEzMzAyNDMxMTAyMzUiLCJhdWQiOiJodHRwczovL2FwaS5leGFtcGxlLmNvbSIsImlhdCI6MTY4Nzc5NzAxNiwiZXhwIjoxNjg3ODgzNDE2LCJhenAiOiI3WDJVWTNDWkUxdnA1OXFCaXZWMUt0eUM3M2toNTdtYyIsInNjb3BlIjoib2ZmbGluZV9hY2Nlc3MifQ.bWA95DkndtQtX6hokDe2IqwWNdWCiEX_HE8ziJvNKAAFWSjIYC2FcszGiV90X_m2emnnoIdd0rKkChmsj4nZp39wfraUI4MBibDUIf6El49agkZxp6SZcUGGWPPzioCUu1WBA2lnBEuxggMsj6Z17eyGDHcuFDS-pQaRhDODB7ZDkMAnm1jjInk8FTI1bBjL8F7Sy39d-7qVw6DMuXar8GEGAYldJgNbqna2BxorHonsjTT3GXFxxZOODhwZPiAf-1MvHcMpgWy1ZRJ-gXj5keYKaIMY8LacnEdKmiuC6klzt1M4YxMBt9ppQJ3cOa0t5_puAyye2OEmaHrMI8xjmQ","scope":"offline_access","expires_in":86400,"token_type":"Bearer"}
```

- PKCE to protect the flow

### Protecting the Access Token in SPA/native app browser
- LocalStorage
- SessionStorage
- Cookies
- HTTP only Session Cookie (backend server)


```env
CODE_VERIFIER=da26c47d1f39ccceb25e4aa43619bd8ac7ed47da462e39b649eb9cd8
CODE_CHALLENGE=OIj1OmcMzWbWyHvpkZ94GA5cFeJf1KxW7kxggGb6Jjs
CLIENT_ID=BldJRrvEIjRRROYgUmBGjinABw0Wo6y0


```


```bash

https://dev-bz-14ku3.us.auth0.com/authorize?
  response_type=code&
  client_id=BldJRrvEIjRRROYgUmBGjinABw0Wo6y0&
  state=abcdefg&
  redirect_uri=https://example-app.com/redirect&
  code_challenge=OIj1OmcMzWbWyHvpkZ94GA5cFeJf1KxW7kxggGb6Jjs&
  code_challenge_method=S256


```


```bash
`code=CVOMSPfAwKqK6y9AVUwz5nY0O_LW5ZVkxFAGJZLEiWB17`

`state=abcdefg`
```

```bash

curl -X POST "https://dev-bz-14ku3.us.auth0.com/oauth/token" \
  -d "grant_type=authorization_code" \
  -d "redirect_uri=https://example-app.com/redirect" \
  -d "client_id=BldJRrvEIjRRROYgUmBGjinABw0Wo6y0" \
  -d "code_verifier=da26c47d1f39ccceb25e4aa43619bd8ac7ed47da462e39b649eb9cd8" \
  -d "code=CVOMSPfAwKqK6y9AVUwz5nY0O_LW5ZVkxFAGJZLEiWB17"

```

```bash

{"access_token":"eyJhbGciOiJSUzI1NiIsInR5cCI6IkpXVCIsImtpZCI6InFEQm1Dc0NmamlsZk9nT0pvNmJNaiJ9.eyJpc3MiOiJodHRwczovL2Rldi1iei0xNGt1My51cy5hdXRoMC5jb20vIiwic3ViIjoiZ29vZ2xlLW9hdXRoMnwxMTYwNDUyNTEzMzAyNDMxMTAyMzUiLCJhdWQiOiJodHRwczovL2FwaS5leGFtcGxlLmNvbSIsImlhdCI6MTY4Nzg1MDY2NywiZXhwIjoxNjg3OTM3MDY3LCJhenAiOiJCbGRKUnJ2RUlqUlJST1lnVW1CR2ppbkFCdzBXbzZ5MCJ9.eme0PwMSgIY2Jid_NXfGVbUyiXVN8M_B5A0e9ntHGnQDvC13dxsaFejjLQroQbyQwr2HYjywJKAyajevv9SvMBWkg2t1ojD1crYcfJvsr9ifRQhhw4r5khTposERjgMs7ajVCQsx8y-42BCENwPQ4oB7O1tR0aq5WwDZQT-2DJGblKsP-1fAim21tH3uEM_b2RZa34s3KNMHrJDgXDlfC0xL01Td3WzmTvMqJw8ifZPGCQdAd30XTzjd1Cmx2Dbw_VLCzNeWLfLZAkUq_P1exTUvoHxyfACOTh3-y9BSO_5q0rCoQISP62BAOMc8oiEShHWXo0nXiywWWI_kYQYjlw","expires_in":86400,"token_type":"Bearer"}

```


### Machine to Machine
```env
CLIENT_ID=DXcXNlXSocsx63xII7VjE0wumUwDyRwD
CLIENT_SECRET=LL4HVCCER_pSaHAQddOfX28RiRwpH-FnqS_MZIj6WZ6rLeiJ4llWwk5uNU9UG9wa
ISSUER_URL=https://dev-bz-14ku3.us.auth0.com
AUTHORIZATION_ENDPOINT=https://dev-bz-14ku3.us.auth0.com/authorize
TOKEN_ENDPOINT=https://dev-bz-14ku3.us.auth0.com/oauth/token

```

```http
curl -X POST "https://dev-bz-14ku3.us.auth0.com/oauth/token" \
  -d "grant_type=client_credentials" \
  -d "client_id=DXcXNlXSocsx63xII7VjE0wumUwDyRwD" \
  -d "client_secret=LL4HVCCER_pSaHAQddOfX28RiRwpH-FnqS_MZIj6WZ6rLeiJ4llWwk5uNU9UG9wa"
```

```json
{"access_token":"eyJhbGciOiJSUzI1NiIsInR5cCI6IkpXVCIsImtpZCI6InFEQm1Dc0NmamlsZk9nT0pvNmJNaiJ9.eyJpc3MiOiJodHRwczovL2Rldi1iei0xNGt1My51cy5hdXRoMC5jb20vIiwic3ViIjoiRFhjWE5sWFNvY3N4NjN4SUk3VmpFMHd1bVV3RHlSd0RAY2xpZW50cyIsImF1ZCI6Imh0dHBzOi8vYXBpLmV4YW1wbGUuY29tIiwiaWF0IjoxNjg3OTU0NDY0LCJleHAiOjE2ODgwNDA4NjQsImF6cCI6IkRYY1hObFhTb2NzeDYzeElJN1ZqRTB3dW1Vd0R5UndEIiwiZ3R5IjoiY2xpZW50LWNyZWRlbnRpYWxzIn0.SReIvdU4liIfSjCona3Cs_LFqN0cIKMDQfw96Cc7RUCOGzvo4bgAVFmiXqfPQk86m8B837Q-xrUtHpoVkY_UE5tS5_eyBfp9iOOM8WCK-wBnWjBpEZ5wHVynAZi9CIsfOh9PbfV8X9hZl4K--jrPOqCDQln93V1VQfreJSFpzIUCIvkhhVBB78wNGf42su6fj6E8GAxPvwRT67WmT6LghOyhmdRMfv4IFm9x7LDC0-E82F0APgoyde1VtA7jd-z7t9DJeQWcgcTblIBLL-GIGY_X3k4mvd_sn4zWZBC3atIbtvhlzzlpsmKWLb6LXrhnRbYNy-Phs-Cx6CqqUBLgAw","expires_in":86400,"token_type":"Bearer"}
```


### OpenID Connect
- JWT encoded not encrypted
	- key id + algo
	- userid, email, issuer, audience, issued at, expiry
- Issuer - auth0 (auth server)
- Audience - who is meant to interpret the token
	- audience of an access token is the api (api checks to make sure it is valid)
	- audience of an id token is the app (app validates the id token and uses its properties)
- Add scope openid to access token request





```env
CLIENT_ID=7X2UY3CZE1vp59qBivV1KtyC73kh57mc
ISSUER_URL=https://dev-bz-14ku3.us.auth0.com
AUTHORIZATION_ENDPOINT=https://dev-bz-14ku3.us.auth0.com/authorize
TOKEN_ENDPOINT=https://dev-bz-14ku3.us.auth0.com/oauth/token
CODE_VERIFIER=080b4871e65004ac77e366c19f9e278a493dec63d9ffb4b224df1d89
CODE_CHALLENGE=r5dVLGqUPHN8gLqizD5Tusv32km7Uv0ANnJE-fD4Q3c
```

1 authorization request
```http
https://dev-bz-14ku3.us.auth0.com/authorize?response_type=code&client_id=7X2UY3CZE1vp59qBivV1KtyC73kh57mc&state=ABCDEFG&scope=openid+profile+email&redirect_uri=https://example-app.com/redirect&code_challenge=r5dVLGqUPHN8gLqizD5Tusv32km7Uv0ANnJE-fD4Q3c&code_challenge_method=S256

```

2 auth code response
```bash
https://example-app.com/redirect?code=-VUjk6an9oKq-9iDnOJZhVN281UDeWWmxH5pAxYtmNi4X&state=ABCDEFG
```

3 access code request
	- post request form encoded post body
```bash
curl -X POST https://dev-bz-14ku3.us.auth0.com/oauth/token \
  -d grant_type=authorization_code \
  -d redirect_uri=https://example-app.com/redirect \
  -d client_id=7X2UY3CZE1vp59qBivV1KtyC73kh57mc \
  -d client_secret=Pei2_VynOaZmrhQy_I9ac0e3AyUmHTYfs1iEnJU7fnTc3DhC_TxzCPSYCXoStxmW \
  -d code_verifier=080b4871e65004ac77e366c19f9e278a493dec63d9ffb4b224df1d89 \
  -d code=-VUjk6an9oKq-9iDnOJZhVN281UDeWWmxH5pAxYtmNi4X
```

4 access code response
```bash
  {"access_token":"eyJhbGciOiJSUzI1NiIsInR5cCI6IkpXVCIsImtpZCI6InFEQm1Dc0NmamlsZk9nT0pvNmJNaiJ9.eyJpc3MiOiJodHRwczovL2Rldi1iei0xNGt1My51cy5hdXRoMC5jb20vIiwic3ViIjoiZ29vZ2xlLW9hdXRoMnwxMTYwNDUyNTEzMzAyNDMxMTAyMzUiLCJhdWQiOlsiaHR0cHM6Ly9hcGkuZXhhbXBsZS5jb20iLCJodHRwczovL2Rldi1iei0xNGt1My51cy5hdXRoMC5jb20vdXNlcmluZm8iXSwiaWF0IjoxNjg4MDc2NTQyLCJleHAiOjE2ODgxNjI5NDIsImF6cCI6IjdYMlVZM0NaRTF2cDU5cUJpdlYxS3R5Qzcza2g1N21jIiwic2NvcGUiOiJvcGVuaWQgcHJvZmlsZSBlbWFpbCJ9.ewoB-xcPcr0Dm5s17qIJOdVOiRkce5saO34xsP1tbTXnS1xGrwG08E7oBHBW117l0yebVtvPMGRWQ6HuQdO1zAfl266xKbf13Zyne2DB2Pgc-n5ESCgG57KiqCbKViq1sUGqbQ585vX0y5A7E5qUDb-cUiYDIgqIVvyVZNL7VP4YLjOlOyiibh6NQHXYFxRiSAKPqxIWAZijQBiGH1kOD83JYT9EQcrx0AjHotcX4iH3GqMqYwfwyOhqqX74LXNyc3YDiBefXXvAoBtlWqo7YMDitucCa0mdmRJtQiqKwS6TUSfkkLM_1Q44SfKpOhDJqNMFkd6no3S0k9c6zjrXfQ","id_token":"eyJhbGciOiJSUzI1NiIsInR5cCI6IkpXVCIsImtpZCI6InFEQm1Dc0NmamlsZk9nT0pvNmJNaiJ9.eyJnaXZlbl9uYW1lIjoiQnJvdGhlciIsImZhbWlseV9uYW1lIjoiTmlmdHkiLCJuaWNrbmFtZSI6ImFkbWluIiwibmFtZSI6IkJyb3RoZXIgTmlmdHkiLCJwaWN0dXJlIjoiaHR0cHM6Ly9saDMuZ29vZ2xldXNlcmNvbnRlbnQuY29tL2EvQUFjSFR0Y1FEc0tKWWJTLWRRbjhJMkh4R3dlUm1hYjZ4OUh4ZWhxVWJ4cDI9czk2LWMiLCJsb2NhbGUiOiJlbiIsInVwZGF0ZWRfYXQiOiIyMDIzLTA2LTI1VDEzOjIwOjM5Ljk5NloiLCJlbWFpbCI6ImFkbWluQGJyb25pZnR5Lnh5eiIsImVtYWlsX3ZlcmlmaWVkIjp0cnVlLCJpc3MiOiJodHRwczovL2Rldi1iei0xNGt1My51cy5hdXRoMC5jb20vIiwiYXVkIjoiN1gyVVkzQ1pFMXZwNTlxQml2VjFLdHlDNzNraDU3bWMiLCJpYXQiOjE2ODgwNzY1NDIsImV4cCI6MTY4ODExMjU0Miwic3ViIjoiZ29vZ2xlLW9hdXRoMnwxMTYwNDUyNTEzMzAyNDMxMTAyMzUiLCJzaWQiOiJFa1h1WjJWTnFHbXVPbnllcXE5MVUxQkVaVHpBbm9nZiJ9.fQ7rk9EiyDC4I0zfmIIF59J6Q-E4QrnD0bYchZrjVT8_l_RID-QFIeEI6iaK6X6xkk4jV7WKZECRZ5cfGVzXe7FYpI0Dn4Qz1Qns_rq7MlJ7COHoTmEoSppEQs532hVQ4nBJpJ_aYF2mtf6iorWCK7WkYaXou-bjYe86NC9aQwgGzaKueu3CVxgDZNBg8yZqSBznSZRRhLj6zvd5gOF2RnSkvXidycAaXuI6ejotyEKV75-vL2mnqr74mzoZDZa4KHkNRetrrk6aeekkJejsouR8PO7l0lqx37xPfUsbHyUhf7anaz2NoROCOJO3I2lEL_N2YQdymYsHmtzkpjxvJg","scope":"openid profile email","expires_in":86400,"token_type":"Bearer"}
```



`code=ku04tWF_myYSbmhil45mmB9nTKIsgdoElOhc9sAvfrLsD`

`state=ABCDEFG`


```http
curl -X POST https://dev-bz-14ku3.us.auth0.com/oauth/token \
  -d grant_type=authorization_code \
  -d redirect_uri=https://example-app.com/redirect \
  -d client_id=7X2UY3CZE1vp59qBivV1KtyC73kh57mc \
  -d client_secret=Pei2_VynOaZmrhQy_I9ac0e3AyUmHTYfs1iEnJU7fnTc3DhC_TxzCPSYCXoStxmW \
  -d code_verifier=080b4871e65004ac77e366c19f9e278a493dec63d9ffb4b224df1d89 \
  -d code=ku04tWF_myYSbmhil45mmB9nTKIsgdoElOhc9sAvfrLsD
```

```json
{"access_token":"eyJhbGciOiJSUzI1NiIsInR5cCI6IkpXVCIsImtpZCI6InFEQm1Dc0NmamlsZk9nT0pvNmJNaiJ9.eyJpc3MiOiJodHRwczovL2Rldi1iei0xNGt1My51cy5hdXRoMC5jb20vIiwic3ViIjoiZ29vZ2xlLW9hdXRoMnwxMDUyMTg5MDQwNDMxOTQ0MTIwNDgiLCJhdWQiOlsiaHR0cHM6Ly9hcGkuZXhhbXBsZS5jb20iLCJodHRwczovL2Rldi1iei0xNGt1My51cy5hdXRoMC5jb20vdXNlcmluZm8iXSwiaWF0IjoxNjg4MzA3MTAzLCJleHAiOjE2ODgzOTM1MDMsImF6cCI6IjdYMlVZM0NaRTF2cDU5cUJpdlYxS3R5Qzcza2g1N21jIiwic2NvcGUiOiJvcGVuaWQgcHJvZmlsZSBlbWFpbCJ9.ABaS9cM6mbdlws83N1FiEwhYFfieVMxuMTGoZV7Lch5zfK5IfO_7a1xI-nLRzkjM2RlaAQ50svKS6UeV_ApAwBAXkA3oOYDdzc6lnnGko2dsk1-bRuSgPOCzo4wj6feUomkbXjai-G7aFIpEdSDXT9xGWmmOaQ1aVBCtabVag-8jdTh3gBEa0dBtzZmZFYZ8yvtKDhqp1EUuUr4jB14BmUslSJ8m-vmMtP6Mt7YUsNsEFe7ASDT2Jv4dFUhNb8-Y6bzysR-zXPAxV0dGdEaLg-h8OGvEwLRFV2gRAgikau9LS324Ik_kuMgi9IdArBgwFdZfSK-sX4BlIbneJYLTUg","id_token":"eyJhbGciOiJSUzI1NiIsInR5cCI6IkpXVCIsImtpZCI6InFEQm1Dc0NmamlsZk9nT0pvNmJNaiJ9.eyJnaXZlbl9uYW1lIjoiQnJvIiwiZmFtaWx5X25hbWUiOiJOaWZ0eSIsIm5pY2tuYW1lIjoiYnJvbmlmdHkiLCJuYW1lIjoiQnJvIE5pZnR5IiwicGljdHVyZSI6Imh0dHBzOi8vbGgzLmdvb2dsZXVzZXJjb250ZW50LmNvbS9hL0FBY0hUdGZaOFpqTmlRaS1IbXBab1hGUndSRmhJX2J2Tl9oOFlsZ0dHMmVKYV80YT1zOTYtYyIsImxvY2FsZSI6ImVuIiwidXBkYXRlZF9hdCI6IjIwMjMtMDctMDJUMTQ6MTA6MTEuMDg2WiIsImVtYWlsIjoiYnJvbmlmdHlAZ21haWwuY29tIiwiZW1haWxfdmVyaWZpZWQiOnRydWUsImlzcyI6Imh0dHBzOi8vZGV2LWJ6LTE0a3UzLnVzLmF1dGgwLmNvbS8iLCJhdWQiOiI3WDJVWTNDWkUxdnA1OXFCaXZWMUt0eUM3M2toNTdtYyIsImlhdCI6MTY4ODMwNzEwMywiZXhwIjoxNjg4MzQzMTAzLCJzdWIiOiJnb29nbGUtb2F1dGgyfDEwNTIxODkwNDA0MzE5NDQxMjA0OCIsInNpZCI6ImhFWlVSNXB6UHJETDV5d2F0X3VKTW5tVUFSeW41bnNkIn0.kYSmadFI682BfC7I2xKSxLb0Q6Seq66hsJIE-azGflEzvz_Rjar2ni5fsbz_A6swqGtK3JBMPjmZ6u58HN5j1l3LejOVa7gnF5OZdG_7A8NlzIqSJ5j9axyfYwItcclXuitMPR5NdTC29cu84r03eHp3TtRYhc-jhJHDLydn-93NS18Cz9tLADK9i_-_1GNFFHKKDdxtKpNnZAKZw_gX8mcxQ59M_LkH_QfMTy5QH-eRa7OXe_6oDVD2-xGTSi1Su3-9P4gzpe6kDAsHMSpvzMmC9G-cTpbMUKmoARFG9RULsBiD8p4GfOK9Az0o8XLEp_eqT16U7xWu5-_ALcjrxA","scope":"openid profile email","expires_in":86400,"token_type":"Bearer"}
```


### JWT 
- Concert analogy for 4 main roles aka "claims" aka declarations (eg of a manifest) in a JWT:  
1) ISSUER: box office serves tickets (oauth server produces access tokens) 
2) AUDIENCE: concert venue takes tickets (resource aka api server validates access tokens)
3) SUBJECT: concertgoer (user of app)
4) CLIENT: digital concierge who procures ticket on behalf of subject & presents it to venue 

- 3 parts (2 dot separators of ey...)
1) header
	- key id 
	- algorithm
2) payload
	- claims
3) signature 
	- encoding

- JWT Profile for OAuth 2.0 Access Tokens standard claims:
	- required:
		- issuer
		- expiry
		- issued at
		- audience
		- subject
		- client id
		- jwt id 
	- optional:
		- scope
		- auth_time
		- auth context class ref
		- auth method ref

- keys and issuer id identified at URL on server metadata endpoint 
	- https://authorization-server.com/.well-known/oauth-authorization-server
	- docs
	- JSON that describes the oauth server like issuer, auth and token endpoints etc
- take JWT plus JWT library and check against the auth server's public key (found in metadata endpoint or docs) and validate whether JWT signature is valid
- next validate the claims
	- iss matches issuer / oauth server api is configured to use (makes sure api isn't accepting tokens from other oauth servers)
	- aud matches audience / resource aka api sercver (makes sure access token is made for this api and not another one)
	- exp and iat - make sure timestamps are not expired or issued in the future
- those are the minimum, you can also check optional things like
	- scopes to make sure the access is scoped correctly...

- Remote Token Introspection v Local Token Validation 
	- Remote lookups create latency & bottleneck on auth server
	- Local validation is scalable, but there is no check for token invalidation before expiry
	- API Gateway is a compromise that is both scalable and secure

### API Gateway
- Token validation logic in API Gateway removes logic from the API itself
- Low risk calls can be handled with local validation
- High risk calls can be proxied to the auth server to do remote token validation
- Alternatively, middleware could perform local validation and api could make a call to remote auth server for extra validation in the high risk cases (eg charging a credit card or otherwise processing an order)
- 