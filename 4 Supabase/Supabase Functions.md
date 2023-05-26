[app.supabase](https://app.supabase.com/project/hstvrsnnpqqyiptiming/database/tables)
```bash
supabase init
supabase login
supabase link --project-ref your-project-ref
supabase functions new hello-world
supabase functions deploy hello-world
#webhook
supabase functions deploy --no-verify-jwt hello-world
```


