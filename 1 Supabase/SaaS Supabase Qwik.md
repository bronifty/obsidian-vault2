[code walkthrough](https://www.youtube.com/watch?v=yBjFpC7dreU&list=PLkswEDcfBXYfRsxzRk5JNWZdr7qL3e6BD)
[supabase auth](https://www.youtube.com/watch?v=_XM9ziOzWk4)
### Policies
- auth.uid()
```plsql
-- Gets the User ID from the request cookie
create or replace function auth.uid() returns uuid as $$
	select nullif(current_setting('request.jwt.claim.sub', true), '')::uuid
$$ language sql stable;
```
- auth.role()
```plsql
-- Gets the User ID from the request cookie
create or replace function auth.role() returns text as $$
	select nullif(current_setting('request.jwt.claim.role', true), '')::text
$$ language sql stable;
```
- auth.email()
```plsql
-- Gets the User ID from the request cookie
create or replace function auth.email() returns text as $$
	select nullif(current_setting('request.jwt.claim.email', true), '')::text
$$ language sql stable;
```

- USING checks before the insert, WITH CHECK is after
```sql
USING (auth.uid() = user_id)
```
this will only allow the logged in user to have access to the rows owned by the user

- enable rls
```sql
alter table public.users enable row level security;
```

- full example
```sql
create policy "Allow logged-in read acces" on public.users for select using (auth.role() = 'authenticated')
```
- another one
```sql
create policy "Allow individual update access" on public.users for update using (auth.uid() = id)
```


### oauth Google
- create a new oauth client on gcp
- use this link to authorize on the client https://ciunmygtgolslwugfgrb.supabase.co/auth/v1/authorize?provider=google
	- once user logs in or chooses the google account, they will be redirected to the app with an access token
	- you can check it on jwt.io


http://localhost:3000/#access_token=eyJhbGciOiJIUzI1NiIsImtpZCI6Ik94bWRPa1FreTVGY2RxVTciLCJ0eXAiOiJKV1QifQ.eyJhdWQiOiJhdXRoZW50aWNhdGVkIiwiZXhwIjoxNjkxOTc2MjYyLCJpYXQiOjE2OTE5NzI2NjIsImlzcyI6Imh0dHBzOi8vY2l1bm15Z3Rnb2xzbHd1Z2ZncmIuc3VwYWJhc2UuY28vYXV0aC92MSIsInN1YiI6ImY2NjY1ZDRjLTcwNDYtNDgwZC1hYzVhLTI5ZDY3NDc5NzcyZiIsImVtYWlsIjoiYWRtaW5AYnJvbmlmdHkueHl6IiwicGhvbmUiOiIiLCJhcHBfbWV0YWRhdGEiOnsicHJvdmlkZXIiOiJlbWFpbCIsInByb3ZpZGVycyI6WyJlbWFpbCIsImdvb2dsZSJdfSwidXNlcl9tZXRhZGF0YSI6e30sInJvbGUiOiJhdXRoZW50aWNhdGVkIiwiYWFsIjoiYWFsMSIsImFtciI6W3sibWV0aG9kIjoib2F1dGgiLCJ0aW1lc3RhbXAiOjE2OTE5NzI2NjJ9XSwic2Vzc2lvbl9pZCI6ImU0ZWJmMzQ0LWQyNzQtNDU3ZC1iMGNlLTEwZWZmNzBmMDcxZCJ9.RLKgXcrpgb-9Po8VNLQdd5Z5ZISn1Pja-lVqh-m9Y2Q&expires_in=3600&provider_token=ya29.a0AfB_byDhUVvz9k8auRukM6WkIXQne-Xojqz2h_aJUWuuNLRt2uQgpSelgztUnPwkkrZciG97qT2l7n_Y56dMPvrSDhhTkFwRn7t0b_Qa5TjCbjbHd4m_1MR5a50YwEdk7SqOdsdINNv489vgihXyqXNNyaqmaCgYKAZ8SARESFQHsvYlsKm7rzbaesAPvAhlxec4SzA0163&refresh_token=mnGSMfI_ksfDj-TtvOcp6A&token_type=bearer


