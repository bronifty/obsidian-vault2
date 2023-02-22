### IoC Containers
- instead of managing codes directly, an IoC (inversion of control) container will manage dependencies, which are injected into the constructor of the app at boot time and to which are delegated tasks

- the IoC container, instead of accessing the browser directly, will delegate to a module which accesses the browser
- Instead of this:
```ts
class App {
	constructor() {
		console.log('some info')
	}
}
```

- We use this:
```ts
class App {
	constructor() {
		dep.get('logger').log('some info')
	}
}
```

App => Container => browser,api,service worker, etc
- the Container will pass the appropriate dependency based on what config setting has been applied to it in the root of the app
- the benefit is to dynamically load source code for:
	- flexibility
	- looser coupling
	- simpler state management
	- easier testing
	- clearer architecture
	- microfrontend compatibility

- static module imports vs dynamic dependencies (runtime / containers)
- you tell the container to get a dependency and pass it a value
	- the config set on the container will determine which dependency (there will be a mapping of these config strings to the dependency modules)

##### static
```ts
import Dependency from 'Dependencies'
class MyClass {
	constructor(){
		Dependency.doStuff()
	}
}
```
##### dynamic
```ts
import Container from 'Container'
class MyClass {
	constructor(){
		Container.get('Dependency').doStuff()
	}
}
```
- the Container is aware of the dependencies (like a metadata catalog in drag and drop SQL reporting); it maintains a data dictionary or mapping of dependency name to module location in the source code
- Container as proxy (takes a request, proxies or delegates, it to a dep)
- 

### OO v Fun Deps
- Source v Runtime Deps

##### oo - source deps
```ts
class SayHello {
	sayHello(){
		console.log('hello')
	}
}

class DependencyInvoker {
	constructor(){
		this.dependency = new SayHello()
	}

	invokeDependency(){
		this.dependency.sayHello()
	}
}

let obj = new DependencyInvoker()
obj.invokeDependency()
```

##### oo - runtime deps
```ts
class SayHello {
	sayHello(){
		console.log('hello')
	}
}

class DependencyInvoker {
	invokeDependency(dependency){
		dependency.sayHello()
	}
}

let obj = new DependencyInvoker()
obj.invokeDependency(new SayHello())
```
- dep passed dynamically (as a reference) at runtime as we invoke

##### fun - source deps
```ts
function sayHello(){
	console.log('hello')
}
function invokeDependency(){
	sayHello()
}
invokeDependency()
```

##### fun - runtime deps
```ts
function sayHello(){
	console.log('hello')
}
function invokeDependency(callback){
	callback()
}
invokeDependency(sayHello)
```

### DI & IOC
- do it yourself
```ts
class MyClass {
	constructor(){
		console.log('do stuff') // prints do stuff
	}
}
```
- have the dependency do it (dependency inversion)
	- normally the dependency is at the lowest level, when you invert it, you are making the dependency a higher level, which calls down further
	- hiding low level implementation details behind a dependency - like a facade pattern - which we call an interface
```ts
// dependency inversion
class MyClass {
	constructor(){
		Dependency.doStuff() // prints do stuff
	}
}
```
- pass it to the container and let it handle the dependency injection, which inverts the dependency (inversion of control)
	- inversion means higher level abstraction rather than the lowest level implementation detail 
	- inversion of control means give control for handling dependency inversion and injection to a container, which has everything set up with mappings to the modules based on parameters you pass it at boot time (very much like a feature flag - eg AWS AppConfig)
		- AWS AppConfig canonical use case is to switch to a db in another AZ if the default AZ - Availability Zone - goes down by passing in params on failover
```ts
// inversion of control
class MyClass {
	constructor(){
		Container.get('Dependency').doStuff() // prints do stuff
	}
}
```
- when we use Singletons for state management, we can house them in the IOC container and it offloads lifecycle management to the container which can deal with it systematically, and programmatically based on parameters
- another benefit is the Container offloads management of the module system and allows us to deal with the Container instead of the imports directly

- With a direct dependency, you install the engine yourself. With Inversion of Control (using a standardized process - via IOC Container), you offload handling of dependencies to the auto shop, who calls the mechanic function to drop in your engine based on your service contract
- IOC allows us to replace source code deps with runtime deps making our software more flexible
- With source code deps, you hardcoded the mechanic (hint: this you), with runtime deps, you deferred control over the handling of deps to the container (auto shop), which is an abstraction because you don't know who the mechanic will be until it happens (mechanic could call in sick or have his schedule changed by an admin; likewise, a mapping in the IOC Container could be changed to call another module dep by default before you run it)

### Dynamic Dep Strats
1 basic
```ts
let logger
if(isBrowser) {
	logger = new BrowserLogger()
}else{
	logger = new NodeLogger()
}
```

2 service location pattern
	- 'service' being a runtime dependency with some function that is called
	- we locate the correct 'service' based on its name (token) and whether the env is server or client 
		- The ideal matrix is to have both a 'client' / browser and 'server' / node version of a named/tokenized 'service' (class whose method is called - or a plain function - to fulfill a contract) 
			- Concretely, we could have in below example, a node server logger and a client browser logger
```ts
let logger = get('logger')
// function takes token and compares to list avail along with env (browser or node) to return correct dep
function get(tokenArg){
	let deps = [
		{token:'logger', env:'browser', dep:BrowserLogger},
		{token:'hasher', env:'browser', dep:BrowserHasher},
		{token:'logger', env:'node', dep:NodeLogger},
	]
	return deps.find((dep) => {
		return dep.env === ENV.env && dep.token == tokenArg
	)}
}
```

- examples
```ts
// AngularJS (Javascript)
UserController.$inject = ['Logger'];
function UserController(Logger){}

// Simple IOC Container (Javascript)
// https://github.com/melamber/simple-ioc-container
const logger = di.get('Logger')

// Inversify and Angular (Typescript)
// http://inversify.io
// Declarative
@Injectable()
class UserService{}
constructor(private logger: ILogger) {}

// Procedural
const logger: ILogger = container.get(Types.ILogger)
```

- Typescript allows for polymorphic code; you can provide an interface that may be implemented multiple ways to fulfill the same contract
	- In our example, the auto shop container calls a mechanic function from the service contract; the implementation of that contract could be either a Dave or Fred mechanic function, either one will be sufficient to fulfill it