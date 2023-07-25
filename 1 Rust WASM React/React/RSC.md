
### Recursive Promise
```typescript

"use server";

async function Row({ chunk }) {
	const { next, value, done } = await chunk;
	if (done) return null;
	return (
		<>
		{value}
		<Row chunk={next()} />
		</>
	);
}

async function read(reader) {
	const { value, done } = await reader.read();
	return {
		value,
		done,
		next: () => read(reader),
	};
}

export async function streamAction() {
	const { body } = await fetch(api);
	return <Row chunk={read(body.getReader())} />;
}

```








