[code raiders qwik full stack saas](https://coderaiders.com/site/view?pkgId=35)
[youtube series](https://www.youtube.com/watch?v=zLHYDY9dAbs&list=PLkswEDcfBXYcl1gW7L5zyCVF9LpGhlOqu)
[code raiders setup reference](https://coderaiders.com/site/view?pkgId=35)


```bash
npm ci
touch .env
```

```bash
SUPABASE_URL=your-supabase-url  
SUPABASE_SECRET_KEY=your-supabase-secret-key
```


### Routing
- **`[id]`** is a directory that represents a dynamic route segment, in this example `id` is the string parameter accessible by `useLocation().params.id`.
- **`[...catchall]`** is a directory that represents a dynamic catch-all route, in this example `catchall` is the string parameter accessible by `useLocation().params.catchall`.
- **`index.tsx|mdx` files** are the pages/endpoints/middleware.
- **`layout.tsx` files** are the layouts.

```
src/routes/blog/index.tsx → /blog

src/routes/user/[username]/index.tsx → /user/:username (/user/foo)

src/routes/post/[...all]/index.tsx → /post/* (/post/2020/id/title)
```


- url segment is state 
	- eg src/routes/user/[username]/index.tsx returns 'username' as the result of a call to useLocation.params which is the key and the value is whatever you visit in the url at the location
		- eg localhost/user/foo will return 'foo' as the value mapped to useLocation.params.username 
```ts
// src/routes/user/[username]/index.tsx
import { component$ } from "@builder.io/qwik";
import { useLocation } from "@builder.io/qwik-city";

export default component$(() => {
	const loc = useLocation();
	return <div>Hello {loc.params.username}!</div>;
});
```


