- [deislabs.io presents WAGI for wasm microservices aka serverless functions](https://deislabs.io/posts/introducing-wagi-easiest-way-to-build-webassembly-microservices/)
- https://github.com/deislabs 
- https://twitter.com/deislabs

##### WAGI Request Response Structure
- When WAGI gets a request, it parses the header into env vars

```rust
fn main() {
    println!("Content-Type: text/plain");
    println!("\n### Env Vars ###");
    std::env::vars().for_each(|v| {
        println!("{} = {}", v.0, v.1);
    });
}
```

- that gets translated into a set of env var key:val pairs like so

```
HTTP_HOST = foo.example.com
SERVER_NAME = foo.example.com
SERVER_SOFTWARE = WAGI/1
GATEWAY_INTERFACE = CGI/1.1
HTTP_USER_AGENT = curl/7.64.1
X_FULL_URL = http://foo.example.com/hello?greet=matt
REQUEST_METHOD = GET
SERVER_PROTOCOL = http
PATH_INFO = /env
HTTP_ACCEPT = */*
SERVER_PORT = 80
PATH_TRANSLATED = /env
QUERY_STRING = greet=matt
```

- we use env vars parsed from request to pass info into wasm modules
- post and put requests with a body send data to modules (WAGI request handlers aka wasm - and specifically system oriented wasm ie., wasi - serverless functions) via STDIN
	- STDIN is a special file handle which can be read with normal file utilities
- here's a Rust WAGI module that echoes the request content type and body

```rust
use std::env::var;
use std::io::{copy, stdin, stdout};

fn main() {
    // Get the content type from the request and echo it
    let default_type = "text/plain".to_string();
    let content_type = var("HTTP_CONTENT_TYPE").unwrap_or(default_type);

    // Echo the message back to the client
    println!("content-type: {}", content_type);
    println!();
    copy(&mut stdin(), &mut stdout());
}
```

- the canonical WAGI server is a Rust web server that answers HTTP requests via CGI, which takes requests, break them into parts, and feed them to shell scripts
	- WAGI takes request body via STDIN, writes to response via STDOUT and handles any headers as env vars so that no wasm module is exposed directly to networking (ports aren't opened liking the code directly to a protocol like ssh or the internet)
		- This makes it a sandboxed POSIX env, which is secure and also sovereign or independent of any other module (eg Singularity container not Docker since in Docker you can elevate privs which is insecure)

---

The WAGI server, written in Rust, provides a web server that answers requests. On each request, it loads the appropriate WebAssembly module, translates the HTTP request to a CGI request, and then runs the module. All of the threading and state management is handled in the WAGI server. A WAGI WebAssembly module just has to handle a single request.

---

##### WAGI File Server
- needs grain (installed)
- build and run locally with grain
- serve in Wagi via hippofactory (build and push bindle and use wagi -b $YOUR_BINDLE)


##### Hippo 
- [hippo-cli](https://github.com/deislabs/hippo-cli)
- hippo is the cli for deployed services
- spin is the tool to build deploy and run services
- 