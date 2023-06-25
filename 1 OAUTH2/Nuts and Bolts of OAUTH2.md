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
- - api (resource server)

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













URL for oauth auth token
```bash
https://dev-bz-14ku3.us.auth0.com/authorize?
  response_type=code&
  client_id={YOUR_CLIENT_ID}&
  state={RANDOM_STRING}&
  redirect_uri=https://example-app.com/redirect&
  code_challenge={YOUR_CODE_CHALLENGE}&
  code_challenge_method=S256
```

oauth response:
```bash
https://example-app.com/redirect?code=FBATjglQ6KuluOFO_R5zW12ahzzfZnYjolTuwzhpFih8_&state=abcdefg
```

post request for access token:
```bash
curl -X POST https://dev-bz-14ku3.us.auth0.com/oauth/token \
  -d grant_type=authorization_code \
  -d redirect_uri=https://example-app.com/redirect \
  -d client_id={YOUR_CLIENT_ID} \
  -d client_secret={YOUR_CLIENT_SECRET} \
  -d code_verifier={YOUR_CODE_VERIFIER} \
  -d code={YOUR_AUTHORIZATION_CODE}
```


```bash
curl -X POST https://dev-bz-14ku3.us.auth0.com/oauth/token \
  -d grant_type=authorization_code \
  -d redirect_uri=https://example-app.com/redirect \
  -d client_id=7X2UY3CZE1vp59qBivV1KtyC73kh57mc \
  -d client_secret=Pei2_VynOaZmrhQy_I9ac0e3AyUmHTYfs1iEnJU7fnTc3DhC_TxzCPSYCXoStxmW \
  -d code_verifier={YOUR_CODE_VERIFIER} \
  -d code={YOUR_AUTHORIZATION_CODE}
```


- auth0 tenant is user db
- 