
- [spiderlightning](https://github.com/bronifty/spiderlightning)

##### Steps to Run Examples
1) Once slight is installed, we need cargo build the rust source setting wasm32-wasi as the build target
2) run slight -c slightfile.toml run -m target/wasm32-wasi/debug/appfilename.wasm

```rust
cargo build --target wasm32-wasi
slight -c slightfile.toml run -m target/wasm32-wasi/debug/restaurant-backend.wasm
```


