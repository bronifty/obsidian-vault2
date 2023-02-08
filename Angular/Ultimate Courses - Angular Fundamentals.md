```shell
npm i -g @angular/cli
ng generate my-project
ng serve
```

[angular cheatsheet 1](https://photos.app.goo.gl/LF33xwkrfHxBQeCU6)
[angular cheatsheet 2](https://photos.app.goo.gl/NVG7MavbVJ4UAgoL6)

##### Pieces
- Modules 
	- group related code together
	- create isolated feature modules
	- code sharing via modules
	- lazy loaded
- Components
	- independent isolated UI functionality
	- reusable composable
	- can render child components
	- one way data flow and immutable state
- Directives
	- add functionality to HTML or component
	- conditionally render
	- dynamically  generate elements and components
	- built-in and custom directives
	- gateway to the DOM; encapsulate DOM manipulation logic
- Pipes
	- functions that transform data for rendering
	- used inside components
	- reusable logic (eg., dates, currency etc)
	- built-in and custom pipes
- Services
	- logic that isn't component-related
	- data fetching and state storage
	- services can be consumed by other services via dependency injection (eg a REST API could consume a database client connection)
	- services are constructed as a Singleton dependency (created once and shared throughout the app wherever they are injected)
- Router
	-  router instructs Angular to render a particular component or module based on the url
	- can create more complex route segments including ids to dynamically fetch data from a database giving us an abstraction to read and write pieces of the URL
	- Directives for active states, click nav etc 
		- Can use this to provide advanced route instructions like params
	- Routes connect -by way of URL- a specific component/module with its data
	- We can intercept a route change to preload data and guard access to features 

