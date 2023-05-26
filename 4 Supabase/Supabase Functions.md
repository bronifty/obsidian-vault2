[app.supabase](https://app.supabase.com/project/hstvrsnnpqqyiptiming/functions)
```bash
supabase init
supabase login
supabase link --project-ref your-project-ref
supabase functions new hello-world
supabase functions deploy hello-world
#webhook
supabase functions deploy --no-verify-jwt hello-world
```

- helpful commands
	- secrets
```bash
#deno env vars
supabase secrets list
supabase secrets set NAME1=value1 NAME2=value2
supabase secrets unset NAME1 NAME2
```
	- local dev
```bash
#docker
supabase start
#http://localhost:54321/functions/v1/hello-world
supabase functions serve
```