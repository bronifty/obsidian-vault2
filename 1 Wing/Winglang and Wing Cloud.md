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


## Phase-independent code[​](https://www.winglang.io/docs/concepts/inflights#phase-independent-code "Direct link to heading")

The global functions `log`, `assert`, `throw`, and `panic` can all be used in both preflight and inflight code.

