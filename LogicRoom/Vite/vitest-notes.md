### Expect Errors
```ts
export function deepMerge(a, b) {

if (Array.isArray(a) && Array.isArray(b)) {

return [...a, ...b];

}

if (Array.isArray(a) || Array.isArray(b) && typeof a !== typeof b) {

throw new Error('cannot merge 2 different types')

}

const merged = { ...a };

// console.log(`--------------`);

// console.log(merged);

for (const key of Object.keys(b)) {

// console.log(`key`, key);

if (typeof a[key] === "object" || Array.isArray(a[key])) {

merged[key] = deepMerge(a[key], b[key]);

} else {

merged[key] = b[key];

// console.log(`merged`, merged);

}

}

// console.log(`merged `, merged);

return merged;

}

  
  

const a = { name: "bronifty", accounts: { github: "unknown" } };

const b = {

name: "bronifty",

accounts: { github: "bronifty", twitter: "bronifty" },

};

// console.log(deepMerge(a, b));
```


```ts
import { test, expect } from "vitest";

import { deepMerge } from "../utils/deepMerge";

  

test("shallow deepMerge with overlap", () => {

expect(

deepMerge(

{ name: "bronifty", github: "unknown" },

{ github: "bronifty", twitter: "bronifty" }

)

).toEqual({ name: "bronifty", github: "bronifty", twitter: "bronifty" });

});

  

test("shallow deepMerge array", () => {

expect(deepMerge(["react", "vue"], ["angular", "svelte"])).toEqual([

"react",

"vue",

"angular",

"svelte",

]);

});

  

test("deepMerge with overlap", () => {

const merged = deepMerge(

{ name: "bronifty", accounts: { github: "unknown" } },

{ name: "bronifty", accounts: { github: "bronifty", twitter: "bronifty" } }

);

expect(merged).toEqual({

name: "bronifty",

accounts: { github: "bronifty", twitter: "bronifty" },

});

});

  

test('throws error on merging two different types', ()=>{

expect(()=> deepMerge(

['foo', 'bar'],

{foo: 'bar'}

)

).toThrowError('cannot merge 2 different types')

})
```

