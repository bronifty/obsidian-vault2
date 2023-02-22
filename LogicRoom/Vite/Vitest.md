- [vitest.dev](https://vitest.dev/)
- [vueschool.io](https://vueschool.io/lessons/expect-for-errors-in-tests)
```bash
npm i vitest
```

- test defines a chunk of functionality you want to test (like 'it' in jest); takes a function as second arg
```ts
// hello.test.js
import {test, expect} from 'vitest'

test('1+1', ()=>{
	expect(1+1).toEqual(2);
})
```

### TDD
- add test
- run all tests; should fail for expected reasons
- write simplest code to pass test
- all tests should now pass
- refactor using test to ensure not regression


### Examples
- deepMerge
```ts
export function deepMerge(a, b) {  
  if (Array.isArray(a)) {  
    // return a.concat(b);  
    return [...a, ...b];  
  }  
  return Object.assign(a, b);  
}

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
  
test("deepMerge array", () => {  
  expect(deepMerge(["react", "vue"], ["angular", "svelte"])).toEqual([  
    "react",  
    "vue",  
    "angular",  
    "svelte",  
  ]);  
});
```


```ts
// export function deepMerge(a, b) {  
//   if (Array.isArray(a)) {  
//     return [...a, ...b];  
//   }  
//   const merged = { ...a };  
//   console.log(`--------------`);  
//   console.log(merged);  
//   for (const key of Object.keys(b)) {  
//     console.log(`key`, key);  
//     if (typeof a[key] === "object" || Array.isArray(a[key])) {  
//       merged[key] = deepMerge(a[key], b[key]);  
//     } else {  
//       merged[key] = b[key];  
//       console.log(`merged`, merged);  
//     }  
//   }  
//   console.log(`merged `, merged);  
//   return merged;  
// }  
  
export function deepMerge(a, b) {  
  if (Array.isArray(a)) {  
    return [...a, ...b];  
  }  
  const merged = { ...a };  
  const stack = [{ merged, b }];  
  
  while (stack.length > 0) {  
    console.log(`stack`, stack);  
    console.log(`merged`, merged);  
    const { merged: currA, b: currB } = stack.pop();  
    for (const key of Object.keys(currB)) {  
      if (  
        typeof currA[key] === "object" &&  
        currA[key] !== null &&  
        typeof currB[key] === "object" &&  
        currB[key] !== null  
      ) {  
        currA[key] = { ...currA[key], ...currB[key] };  
        stack.push({ merged: currA[key], b: currB[key] });  
      } else {  
        currA[key] = currB[key];  
      }  
    }  
  }  
  
  return merged;  
}  
  
const a = { name: "bronifty", accounts: { github: "unknown" } };  
const b = {  
  name: "bronifty",  
  accounts: { github: "bronifty", twitter: "bronifty" },  
};  
console.log(deepMerge(a, b));
```

```ts
// a, b

// merged {...a}

// stack [merged, b]

// currA, currB

export function deepMerge2(a,b){

if(Array.isArray(a)){

return [...a,...b];

}

const merged = {...a};

const stack = [{merged,b}];

while(stack.length > 0){

const {merged:currA, b:currB} = stack.pop();

for(const key of Object.keys(currB)){

if(typeof currA[key]==='object'||Array.isArray(currA[key])){

currA[key] = {...currA[key], ...currB[key]}

stack.push({merged:currA[key], b:currB[key]})

} else {

currA[key] = currB[key];

}

}

}

return merged;

}

  

const a = {name:'bronifty', accounts:{github:'unknown'}}

const b = {name:'bronifty', accounts:{github:'bronifty', twitter:'bronifty'}}

console.log(deepMerge2(a,b))
```


