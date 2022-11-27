- [wasi youtube](https://www.youtube.com/watch?v=97yJtD5FKe4)
- [cosmonic wasm](https://labs.cosmonic.com/)
- [svelte and rust wasm](https://blog.logrocket.com/integrating-svelte-app-rust-webassembly)
- [docker wasm](https://www.docker.com/blog/docker-wasm-technical-preview/)
- [wasmtime](https://docs.wasmtime.dev/tutorial-create-hello-world.html)
- [fermyon.com serverless wasm](https://www.fermyon.com/spin)
- [bytecode alliance for wasm + wasi](https://bytecodealliance.org/)
- [deis labs](https://deislabs.io/posts/wagi-updates/)
- [lunatic.solutions - erlang style wasm runtime](https://lunatic.solutions/)


- wasm browser
- wasi server
- wagi http handlers (serverless functions, which use wasi)

##### WAGI
- exposes HTTP request info to a WebAssembly (WA) module as env vars via reading from stdin and writing to stdout
- [deis labs github wagi repo](https://github.com/deislabs)

##### Hippo WASM PAAS
- [hippo docs](https://docs.hippofactory.dev/)
- i've got a bit of a chicken and egg problem with trying to get the function to run with spin since it's not building due to a lack of a main method, which isn't required if it's just a serverless function, so i can't use hippo to deploy it
	- so let's go to the fermyon cloud to skip the infrastructure setup while i'm still learning the framework and stuff
	- 