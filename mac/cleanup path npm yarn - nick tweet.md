---
author: "bro nifty birds are not real 🐦"
handle: "@bronifty"
source: "https://twitter.com/bronifty/status/1596757194586062848"
date: "November 27, 2022 1:45 AM"
fetched: "November 27, 2022 2:11 AM"
likes: 0
retweets: 0
replies: 3
---
![bronifty](https://pbs.twimg.com/profile_images/1422968961679994884/fA6eIyFm_normal.jpg)
bro nifty birds are not real 🐦 ([@bronifty](https://twitter.com/bronifty)) - November 27, 2022 1:45 AM

Would wiping PATH env var in Mac to start from scratch be equivalent to deleting System32? Has anyone done it? I want to uninstall all npm yarn and brew casks to clean up my system …

[Tweet link](https://twitter.com/bronifty/status/1596757194586062848)

---

![nickemccurdy](https://pbs.twimg.com/profile_images/1270769009420636161/qxqgkg8X_normal.jpg)
Nick McCurdy ([@nickemccurdy](https://twitter.com/nickemccurdy)) - November 27, 2022 1:51 AM

[@bronifty](https://twitter.com/bronifty) Did you install Node and Yarn through Homebrew?

[Tweet link](https://twitter.com/nickemccurdy/status/1596758562893893633)

---

![bronifty](https://pbs.twimg.com/profile_images/1422968961679994884/fA6eIyFm_normal.jpg)
bro nifty birds are not real 🐦 ([@bronifty](https://twitter.com/bronifty)) - November 27, 2022 1:58 AM

[@nickemccurdy](https://twitter.com/nickemccurdy) I’m not exactly sure. Everything works but it’s messy.

[Tweet link](https://twitter.com/bronifty/status/1596760282432667648)

---

![nickemccurdy](https://pbs.twimg.com/profile_images/1270769009420636161/qxqgkg8X_normal.jpg)
Nick McCurdy ([@nickemccurdy](https://twitter.com/nickemccurdy)) - November 27, 2022 2:03 AM

[@bronifty](https://twitter.com/bronifty) I would clear out the npm and yarn directories, reinstall Node through Homebrew, and enable corepack (if you haven’t already) for Yarn

[Tweet link](https://twitter.com/nickemccurdy/status/1596761563448627202)



### Corepack Notes
- [what is corepack fek.io](https://fek.io/blog/what-is-corepack-in-node-js)
```shell
corepack prepare yarn@stable --activate
yarn set version stable
yarn init -2
```
- corepack requires an activation statement like python venv 
- yarn node is the interpreter
	- for example, assuming index.js is the node.js server file: to run node index.js
```shell
yarn node index
```
- dependencies are installed in the .yarn folder of the user directory instead of locally in a node_modules folder and the packages are kept in .zip files like a java archive (jar file)
- to add dependencies, refer to it with corepack prefix like so
```shell
corepack yarn add axios
```

- If you want the node_modules folder (so node will be the interpreter not yarn and you have a direct link to the modules for compatibility), add the following line to .yarnrc.yml:
```yml
nodeLinker: node-modules
```

### Yarn Docs
- [yarnpkg.com](https://yarnpkg.com/getting-started/usage)
- 