
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

