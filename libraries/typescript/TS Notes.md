- tsconfig.json to allow imports
```javascript
{
  "compilerOptions": {
    "esModuleInterop": true,
  }
}
```

- after ts-node is installed

```javascript
npm install -g ts-node
```

- the command to compile and run ts on the file with imports
```shell
ts-node-esm src/typeTest4.ts
```

