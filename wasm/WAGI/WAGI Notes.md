- [deislabs.io presents WAGI for wasm microservices aka serverless functions](https://deislabs.io/posts/introducing-wagi-easiest-way-to-build-webassembly-microservices/)

##### WAGI Request Response Structure
- When WAGI gets a request, it parses the header into env vars
```rust
```rust
fn main() {
    println!("Content-Type: text/plain");
    println!("\n### Env Vars ###");
    std::env::vars().for_each(|v| {
        println!("{} = {}", v.0, v.1);
    });
}
```
```