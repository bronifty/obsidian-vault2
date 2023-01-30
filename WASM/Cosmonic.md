Portal Link: [app.cosmonic.com/dashboard](https://app.cosmonic.com/dashboard)

### Architecture
- Hashicorp Nomad Vault + Consul Control Plane for Firecracker VMs run by Cosmonic https://www.youtube.com/watch?v=U1zn5OO8dHs&t=38s
- Hashicorp Consul, Vault and Nomad are the control plane for Cosmonic to host apps on Firecracker hosts
- Task Driver is a customizable framework which provides a runtime to execute workloads
	- Types of runtimes are: Docker, QEMU, Podman, etc
	- Note: wasm runtime does work inside of Docker containers in place of runc, which is a containerd runtime for linux
- Nomad has a built-in framework for authoring customer task driver plugins
- Firecracker is a vm framework to run KVM vms built by AWS which runs Lambda and Fargate and allows minimalistic implementation geared toward security and multi-tenancy
- cosmonic workflow with firecracker vm is to host thousands of these on a single node and make calls to the metadata service via REST API hosted natively on firecracker vm; metadata gets the cloud-init
- we pass templates into the firecracker micro-vm at runtime via the HTTP REST call over linux socket to the metadata management server
- Uses CNI (community networking interface) with Cillium on Nomad; less Kubernetes, more flexibility
- Future of Nomad and wasmCloud at Cosmonic
	- Comms from task driver to Firecracker VM via agent in VM with vsock support
	- Pull ZFS snapshots on demand (bindle - aka bundle of binaries, similar to Docker layers)
- Nomad and WASM community
	- wasmtime as task driver

- Links: 
	- [nomadproject.io/docs](https://developer.hashicorp.com/nomad/docs/concepts/plugins/task-drivers)
	- [webassembly.org](https://webassembly.org/)
	- [firecracker-microvm.github.io](https://firecracker-microvm.github.io/)
	- [wasmcloud.dev](https://wasmcloud.dev/)


### Cosmonic Docs
- [getting started](https://cosmonic.com/docs/user_guide/developer_loop/#change)
- cosmo launch provides the wasmCloud host for the actor to run
	- actor is code
	- host is the runtime
	- provider is the http server
	- wormhole is the port
	- 




From Discord with Stephen:
-  So I may say Actor is business logic, and provider is general processes that business logic can leverage
- And an interface is the manner in which the actor communicates with the provider