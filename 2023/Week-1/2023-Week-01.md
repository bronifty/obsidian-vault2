
### TS
- [joe previte react-ts build an avatar](https://www.typescriptcourse.com/tutorials/build-a-dynamic-avatar-component-with-react-typescript/set-up-testing-with-vitest-and-testing-library)
https://github.com/bronifty/react-ts-vitest-joe-previte-dynamic-avatar
```bash
yarn create vite my-app --template react-ts
yarn add -D vitest @testing-library/react @testing-library/jest-dom jsdom
```

### EdgeDB
- [build a nextjs blog backed by edgedb](https://www.youtube.com/watch?v=G17Xc8cnSI0)


### Qwik City + Partytown
- [qwik intro tut](https://qwik.builder.io/tutorial/introduction/resource/)
- useState = useStore()
1.  store the state of the component
2.  present the state as a proxy that can observe read/writes to the store
3.  serialize the state of the store into JSON on application pause.
4.  observe which properties of the store are used in a component template and create subscriptions to the store. The subscriptions are then used to automatically update the component template if a store changes.
