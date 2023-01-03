- [wasm preview](https://www.docker.com/blog/docker-wasm-technical-preview/)

```shell
docker run -dp 8080:8080 --name=wasm-example --runtime=io.containerd.wasmedge.v1 --platform=wasi/wasm32 michaelirwin244/wasm-example
```

[docker docs wasm](https://docs.docker.com/desktop/wasm/)

