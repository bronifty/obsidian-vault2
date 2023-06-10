[sb docs functions quickstart](https://supabase.com/docs/guides/functions/quickstart)
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
- when you create a new function (supabase functions new), each function will give you the invocation command with the correct jwt
- deployed functions are going to use your public key, but private functions require the signature ( i think)

### hello world
```ts
// Follow this setup guide to integrate the Deno language server with your editor:
// https://deno.land/manual/getting_started/setup_your_environment
// This enables autocomplete, go to definition, etc.
import { serve } from "https://deno.land/std@0.168.0/http/server.ts"
console.log("Hello from Functions!")
serve(async (req) => {
const { name } = await req.json()
const data = {
message: `Hello ${name}!`,
}
return new Response(
JSON.stringify(data),
{ headers: { "Content-Type": "application/json" } },
)
})
  
// To invoke:
// curl -i --location --request POST 'https://hstvrsnnpqqyiptiming.functions.supabase.co/hello-world' \
// --header 'Authorization: Bearer eyJhbGciOiJIUzI1NiIsInR5cCI6IkpXVCJ9.eyJpc3MiOiJzdXBhYmFzZSIsInJlZiI6ImhzdHZyc25ucHFxeWlwdGltaW5nIiwicm9sZSI6ImFub24iLCJpYXQiOjE2ODQ5MzM2MjEsImV4cCI6MjAwMDUwOTYyMX0.T8I0GHR40TIYApvQl7q3NC6e7iAtneFDU3lV0VxeAEg' \
// --header 'Content-Type: application/json' \
// --data '{"name":"Functions"}'
```

### client.mjs
```ts
// https://supabase.com/docs/reference/javascript/installing
// import { createClient } from '@supabase/supabase-js'
import { createClient } from 'https://esm.sh/@supabase/supabase-js@2'
// Create a single supabase client for interacting with your database
const supabase = createClient('https://hstvrsnnpqqyiptiming.supabase.co', 'eyJhbGciOiJIUzI1NiIsInR5cCI6IkpXVCJ9.eyJpc3MiOiJzdXBhYmFzZSIsInJlZiI6ImhzdHZyc25ucHFxeWlwdGltaW5nIiwicm9sZSI6ImFub24iLCJpYXQiOjE2ODQ5MzM2MjEsImV4cCI6MjAwMDUwOTYyMX0.T8I0GHR40TIYApvQl7q3NC6e7iAtneFDU3lV0VxeAEg')

const { data, error } = await supabase.functions.invoke('hello-world', {
body: { name: 'Functions' },
})

console.log(data);
```

### types
```bash
supabase gen types typescript --project-id=your-project-ref --schema=storage,public > supabase/functions/huggingface-image-captioning/types.ts
```

```ts
// To invoke:
// curl -i --location --request POST 'http://localhost:54321/functions/v1/database-webhook' \
// --header 'Authorization: Bearer eyJhbGciOiJIUzI1NiIsInR5cCI6IkpXVCJ9.eyJpc3MiOiJzdXBhYmFzZS1kZW1vIiwicm9sZSI6ImFub24iLCJleHAiOjE5ODM4MTI5OTZ9.CRXP1A7WOeoJeXxjNni43kdQwgnWNReilDMblYTn_I0' \
// --header 'Content-Type: application/json' \
// --data '{"name":"Functions"}'

// Generate typesafe API from Database schema
// supabase gen types typescript --project-id=kfktzpdexagrkylcxfhj --schema=storage,public > supabase/functions/database-webhook/types.ts
```
