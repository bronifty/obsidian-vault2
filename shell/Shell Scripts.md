
# jq
- add type module to package.json 
```shell
jq '.type += "module"' package.json > temp && mv temp package.json
```
- add start script to package.json
```shell
jq '.scripts += {"start":"node index"}' package.json > temp && mv temp package.json
```
- add dev script to package.json
```shell
jq '.scripts += {"dev": "nodemon index"}' package.json > temp && mv temp package.json
```
- delete dev script from package.json
```shell
jq 'del(.scripts.dev)' package.json > temp && mv temp package.json
```

