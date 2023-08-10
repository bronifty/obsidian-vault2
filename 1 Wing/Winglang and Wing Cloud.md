- [winglang learn](https://www.winglang.io/learn/)

```ts
bring cloud;
bring ex;

class Store {
	b: cloud.Bucket;
	// r: ex.Redis;

	init() {
		this.b = new cloud.Bucket();
	// this.r = new ex.Redis();
	}

	inflight store(data: str){
		this.b.put("data.txt", data);
	// this.r.set("data", data);
	}
}

let myStore = new Store() as "my store";
let yourStore = new Store() as "your store";

new cloud.Function(inflight () => {
	myStore.store("hello world!");
	yourStore.store("hello world 123!");
});
```


### Preflight
- preflight code doesn't require a decorator because it is default
- preflight code can be used to config svcs or setup complex event listeners


```ts
bring cloud;

class ReplayableQueue {
queue: cloud.Queue;
bucket: cloud.Bucket;
counter: cloud.Counter;
  
init(){
this.queue = new cloud.Queue();
this.bucket = new cloud.Bucket();
this.counter = new cloud.Counter();
}

setConsumer(fn: inflight (str): str){
this.queue.setConsumer(fn);
}

inflight push(m: str){
this.queue.push(m);
this.bucket.put("messages/${this.counter.inc()}", m);
}

inflight replay(){
for i in this.bucket.list() {
this.queue.push(this.bucket.get(i));
}
}
}

let rq = new ReplayableQueue();
```


### Inflight Class Context
- inflight class can only contain inflight members (properties and methods); it is safe to create in inflight context
```ts
inflight () => {
  class Person {
    name: str;
    age: num;

    init(name: str, age: num) {
      this.name = name;
      this.age = age;
    }

    inflight greet() {
      log("Hello, ${this.name}!");
    }
  }

  let p = new Person("John", 30);
  p.greet();
};
```

## Phase-independent code[​](https://www.winglang.io/docs/concepts/inflights#phase-independent-code "Direct link to heading")
- inflight and preflight are mutual exclusive except for the phase independent codes
- The global functions `log`, `assert`, `throw`, and `panic` can all be used in both preflight and inflight code.

### Immutable cross-phase references

- Inflight code can reference data like global variables and class fields from preflight, but the data cannot be mutated.
```ts
let var count = 3;
let names = MutArray<str>["John", "Jane", "Joe"];

count = count + 1; // OK
names.push("Jack"); // OK

inflight () => {
  count = count + 1; // error: Variable cannot be reassigned from inflight
  names.push("Jill"); // error: variable "names" cannot be mutated in inflight - error message not 
                      // implemented yet, see https://github.com/winglang/wing/issues/3069
};
```

