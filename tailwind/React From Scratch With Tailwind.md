
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
- import the App and render it with root
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

