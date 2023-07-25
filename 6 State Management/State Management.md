
- nanostores
- react context

### Nanostores
- store
```js
// store.js
import { atom } from "nanostores";
export const atomScore = atom(0);
```
- writer
```js
// Game.jsx
import { atomScore } from "../store/store.js";

export default function Game() {
	atomScore.set(atomScore.get() + 1);
}

```
- reader
```js
// GameOver.jsx
import { atomScore } from "../store/store.js";
import { useStore } from "@nanostores/react";

export default function GameOver() {
// make the store reactive with useStore hook
const atomScoreStore = useStore(atomScore);

	return (
		<div>
			<h1>Game Over</h1>
			<p>nanostore Score: {atomScoreStore}</p>
		</div>
	);
}
```

### React Context
- store
```js
// StoreContext.jsx
import React from "react";

const ScoreContext = React.createContext(0);
const useScoreContext = () => React.useContext(ScoreContext);

const ScoreProvider = ({ children }) => {
	const [score, setScore] = React.useState(0);

	return (
		<ScoreContext.Provider value={[score, setScore]}>
			{children}
		</ScoreContext.Provider>
	);
};

export { ScoreProvider, useScoreContext };

// main.jsx
ReactDOM.createRoot(document.getElementById("root")).render(
	<React.StrictMode>
		<ScoreProvider>
			<RouterProvider router={router} />
		</ScoreProvider>
	</React.StrictMode>
);

```
- writer
```js
import { useScoreContext } from "../store/ScoreContext";

export default function Game() {
	const [score, setScore] = useScoreContext();
	setScore((prevScore) => prevScore + 1);
}
```
- reader
```js
import { useScoreContext } from "../store/ScoreContext";
  
export default function GameOver() {
	const [score, setScore] = useScoreContext();

	return (
		<div>
			<h1>Game Over</h1>
			<p>context Score: {score}</p>
		</div>
	);
}
```
