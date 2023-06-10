
- image is a wrapper around the asset; asset is physical file
- most basic way to use image is reference asset url
```Astro
---

import {useSanityClient} from 'astro-sanity'

export const tempGetPostImageUrls = async () => {

const posts = await useSanityClient().fetch(`*[_type == "post"]{

"imageUrl": mainImage.asset->url,

}`)

return posts;

}

const posts = await tempGetPostImageUrls();

---

  

<ul>

{posts.map((post) => (

<li>

<img src={post.imageUrl} />

</li>

))}

{JSON.stringify(posts, null, 2)}

</ul>
```

