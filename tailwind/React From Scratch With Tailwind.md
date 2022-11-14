
- [pr.new (Stackblitz)](https://stackblitz.com/~/github.com/bronifty/react-from-scratch-with-tailwind-dark-mode)

##### Setup Project Dependencies + File System
```shell
npm init -y
yarn add react react-dom react-scripts
```

```shell
mkdir src public
touch src/index.js src/App.js src/index.css public/index.html
```

##### Fill Out App Bootstrap Codes
- create the vdom root with ReactDOM
- import the App and index.css and render App with root
- export the default App
- add react-scripts start to package.json
```Shell
yarn start
```
- yes to add browser targets

##### Install Tailwind
- [cra](https://tailwindcss.com/docs/guides/create-react-app)
```shell
yarn add -D tailwindcss postcss autoprefixer
npx tailwindcss init -p
```
- update tw config
```javascript
/** @type {import('tailwindcss').Config} */
module.exports = {
  content: [
    "./src/**/*.{js,jsx,ts,tsx}",
  ],
  theme: {
    extend: {},
  },
  plugins: [],
}
```
- update index.css
```css
@tailwind base;
@tailwind components;
@tailwind utilities;
```
- tw is now installed; you can test it by adding some classes to jsx

##### Customize Tailwind by Adding Dark Mode and Prettier Plugin
- [add dark mode class to tw config](https://tailwindcss.com/docs/dark-mode)
``` js
/** @type {import('tailwindcss').Config} */
module.exports = {
	darkMode: "class",
	content: ["./src/**/*.{js,jsx,ts,tsx}"],
	theme: {
	extend: {},
},
plugins: [],
};
```
- all set; now adding dark: pseudo-selector to a class will toggle that value when html root element has class='dark'
- [class sorting with prettier plugin](https://tailwindcss.com/docs/editor-setup#automatic-class-sorting-with-prettier)
	- [github tw prettier plugin](https://github.com/tailwindlabs/prettier-plugin-tailwindcss)
```shell
yarn add -D prettier prettier-plugin-tailwindcss
touch prettier.config.js
```
- add tw plugin to local prettier config
```js
// prettier.config.js
module.exports = {
  plugins: [require('prettier-plugin-tailwindcss')],
}
```
- all set; if prettier is already installed, this plugin will auto-swap your class order to match the standardized preferences

##### Add Theme Button Toggle Logic
```js
// BtnToggleTheme.js
import React from "react";

export default () => {
// track theme in state
// useEffect to trigger local storage update and document class update
// toggle theme function
	const [theme, setTheme] = React.useState(
		localStorage.getItem("theme") || "dark"
	);

	React.useEffect(() => {
		localStorage.setItem("theme", theme);
		document.documentElement.className = "";
		document.documentElement.classList.add(theme);
	}, [theme]);

	const toggleTheme = () => {
		setTheme(theme === "dark" ? "light" : "dark");
	};

	return <button onClick={toggleTheme}>Click me</button>;
};
```
- add anti-FUOC logic to document head
```html
<!-- index.html -->
<head>
<!-- ... -->
<!-- prevent FUOC -->
	<script>
		(() => {
			try {
				let mode = localStorage.getItem("theme");
				if (!mode) return;
				document.documentElement.classList.add(mode);
			} catch (error) {
				console.error(error);
			}
		})();
	</script>
</head>
```
- demo the class method of dark mode theme switching by adding a dark pseudo-selector to the App element
```js
// App.js
import React from "react";
import BtnToggleTheme from "./components/BtnToggleTheme";

export default () => {
	return (
	<div className="bg-sky-300 px-4 dark:bg-sky-900">
	<h1>React 18</h1>
	<BtnToggleTheme />
	</div>
	);
};
```

