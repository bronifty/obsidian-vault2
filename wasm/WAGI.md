- [hippo docs](https://docs.hippofactory.dev/topics/webassembly/)
- A Wagi-compliant server can be configured with mappings that define a WebAssembly binary that should handle a path, such as all requests for `/calculator` are routed to `calculator.wasm`
	- The WebAssembly module is executed for each incomming request, and its output is sent back as the response to the client.
- Wagi was created by [Deis Labs](https://deislabs.io/), and is currently defined in a few documents that build off of CGI and highlight the differences:
-   [WAGI Architecture](https://github.com/deislabs/wagi/blob/main/docs/architecture.md)
-   [WAGI Environment Variables](https://github.com/deislabs/wagi/blob/main/docs/environment_variables.md)
-   [Writing a WAGI Module](https://github.com/deislabs/wagi/blob/main/docs/writing_modules.md)

#### Deis Labs Introduces WAGI
- [waggy like a dog tail is FAAS or CGI for wasm](https://deislabs.io/posts/introducing-wagi-easiest-way-to-build-webassembly-microservices/)
- background: Krustlet is a k8s kubelet (node listener executes workload jobs in a docker container from the orchestrator - aka control - plane) for wasm
	- Krustlet wasm is for long-running jobs (eg Fargate)
	- WAGI (waggy) is for serverless HTTP, short run jobs (eg Lambda)
	- [Krustlet](https://github.com/deislabs/krustlet), a Kubernetes Kubelet that executes WebAssembly payloads instead of Docker containers
	- [WebAssembly Gateway Interface (WAGI)](https://github.com/deislabs/wagi). Pronounced “waggy” (and inspired by [some](https://deislabs.io/images/moar_puppy.jpg) of the [puppies](https://deislabs.io/images/puppy.jpg) on our team), WAGI is the easiest way to build WebAssembly microservices.
	- WAGI is for writing HTTP response handlers. It uses the [WASI](https://wasi.dev/) POSIX-like system to expose an HTTP request to a WebAssembly module. Rather than requiring developers to learn new frameworks or work directly with network sockets, WAGI uses basic features like environment variables and files.

#### Canonical Hello World Example with WAGI

```rust
fn main() {
    println!("Content-Type: text/plain");
    println!();
    println!("Hello world");
}
```
-   Write output to STDOUT
-   Read environment variables for request information
-   Accept uploads through STDIN

### WRITING OUTPUT
Sending data to a web client is as easy as writing to `STDOUT`. Like a regular HTTP message, there are two parts to a WAGI response:
-   A set of headers, which must contain either a `content-type` or a `location`.
-   A body

- Between the two is an empty line. Our Rust code above sent this to `STDOUT`:
```
Content-Type: text/plain

Hello world
```
- While there are a few other headers you can set, the only one you need is `Content-Type` (which is not case-sensitive).
