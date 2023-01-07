- [hippo docs quickstart](https://docs.hippofactory.dev/intro/quickstart/)

##### introducing spin bindle bindle-server hippo and wagi
- spin is wasm compiler
- bindle is a wasm database used by hippo
- hippo deploys wasm
- wagi is wasm api server / reverse proxy / serverless http protocol for request endpoints

##### Bindle
- apps are bundled as bindles; bindles are collected in a searchable bindle-server
- hippo uses bindle under the hood for storing and organizing apps
	- bindle server is app store db
- hippo will publish app revisions to bindle
- to start the server:
```shell
bindle-server --unauthenticated
```