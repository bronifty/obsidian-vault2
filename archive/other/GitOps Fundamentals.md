https://learning.codefresh.io/path-player?courseid=gitops-with-argo&unit=gitops-with-argo_6177d6524a6baUnit

1. code pushed to git
2.  ci build container and pushes to registry
3. second git repo for the manifests
4. trunk dev against second repo
5. gitops controller inside cluster monitors manifest repo and matches cluster state to repo manifest state

a) update plugin in fastify app;
b) push code to git;
c) ci builds container image and pushes to registry;
d) update second repo with new container version and push to git
e) open pull request on main in second repo with changes just pushed to dev branch
f) merge pull request
g) kubernetes cluster automatically pulls second repo updates and updates state of its clusters with the new container version


# Helm
Helm is a package manager for Kubernetes. It’s a convenient way for packaging collections of YAML files with a Helm chart for the Kubernetes application and allowing distribution with a Helm repository.  

When using Helm, there are some powerful commands you will find useful.  
  
To install a new package:

```shell
helm install release_name and name_of_chart_you_want_to_install
```

helm package > chart > artifacts (deployment and service)

The chart install performs the Kubernetes deployment and service creation of the application. The chart is essentially a collection of files used to describe a set of Kubernetes resources, and Helm manages the creation of these resources.  
  
To confirm the deployment and view the currently deployed release:

```shell
helm list
```

or

```shell
helm ls
```

To view revision numbers for a particular release:

```shell
helm history release_name
```

To execute a rollback:

```shell
helm rollback release_name revision_id
```

To upgrade a release:

```shell
helm upgrade release_name chart_name
```

To learn more details about these commands, visit Helm’s official documentation.

export interface Post {
	id?: number;
	text?: string
}


once argocd is used to install the helm it renders it as a template substituting the values and in order to access the helm install you use the argo cli instead of the helm cli

```shell
argocd app list 
argocd app get helm-gitops-example 
argocd app history helm-gitops-example
```


```shell
argocd app create demo \ --project default \ --repo https://github.com/codefresh-contrib/gitops-certification-examples \ --path "./helm-app/" \ --sync-policy auto \ --dest-namespace default \ --dest-server https://kubernetes.default.svc
```

