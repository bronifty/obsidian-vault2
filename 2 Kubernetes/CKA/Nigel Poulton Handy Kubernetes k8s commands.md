https://nigelpoulton.com/webassembly-on-kubernetes-ultimate-hands-on/


```markup
k3d cluster create wasm-cluster \
--image ghcr.io/deislabs/containerd-wasm-shims/examples/k3d:v0.5.1 \
-p "8081:80@loadbalancer" --agents 2
```
```markup
docker exec -it k3d-wasm-cluster-agent-0 ash
```
```css
 kubectl get nodes --show-labels
```
```markup
kubectl label nodes k3d-wasm-cluster-agent-0 spin=yes
```
```markup
kubectl apply \
-f https://raw.githubusercontent.com/nigelpoulton/spin1/main/app.yml
```
```markup
kubectl get deploy
```
```markup
kubectl get pods -o wide
```
```markup
kubectl scale --replicas 3 deploy/wasm-spin
```

```markup
docker exec -it k3d-wasm-cluster-agent-0 ash
```
```markup
ps | grep spin
```

