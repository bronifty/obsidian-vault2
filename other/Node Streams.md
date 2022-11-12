https://nodesource.com/blog/understanding-streams-in-nodejs/

```javascript
let fs = require('fs');
let readableStream = fs.createReadStream('./file.txt', { encoding: 'utf8' });
let data = '';
let chunk;

readableStream.on('readable', function() {
    while ((chunk=readableStream.read()) != null) {
        data += chunk;
    }
});

readableStream.on('end', function() {
    console.log(data)
});

readableStream.on('error', (error) => {
console.log(error.stack);
});
```

- The read() function reads some data from the internal buffer and returns it. When there is nothing to read, it returns null. So, in the while loop, we check for null and terminate the loop. Note that the readable event is emitted when a chunk of data can be read from the stream.

- `Readable` streams begin in **paused mode** but can be switched to **flowing mode** in one of the following ways:
	-   Adding a 'data' event handler.
	-   Calling the `stream.resume()` method.
	-   Calling the `stream.pipe()` method to send the data to a Writable.

- The `Readable` can switch back to paused mode using one of the following:
	-   If there are no pipe destinations, by calling the `stream.pause()` method.
	-   If there are pipe destinations, by removing all pipe destinations. Multiple pipe destinations may be removed by calling the `stream.unpipe()` method.

-  `Readable` will not generate data until a mechanism for either consuming or ignoring that data is provided. If the consuming mechanism is disabled or taken away, the `Readable` will _attempt_ to stop generating the data. 
	- Adding a `readable` event handler automatically make the stream to stop flowing, and the data to be consumed via `readable.read()`. 
	- If the 'readable' event handler is removed, then the stream will start flowing again if there is a 'data' event handler.

```javascript
import fs from 'fs';
var readableStream = fs.createReadStream('file.txt', 'utf8');
var data = '';
var chunk; 

// readableStream.on('readable', function () {
// while ((chunk = readableStream.read()) != null) {
// data += chunk;
// }
// });

readableStream.on('data', (chunk) => {
data += chunk;
console.log('chunk', Buffer.from(chunk).toString());
});

readableStream.on('end', function () {
console.log(data);
});
/*
$ node stream1.js 
chunk 1 Lorem ipsum dolor sit amet, consectetur adipisicing elit. 
2 Veniam vero voluptas sit aliquam consequuntur beatae, 
3 repellendus laborum? Totam non esse sit. Fugit consequuntur 
4 quas a commodi doloremque omnis iure nisi.

1 Lorem ipsum dolor sit amet, consectetur adipisicing elit. 
2 Veniam vero voluptas sit aliquam consequuntur beatae, 
3 repellendus laborum? Totam non esse sit. Fugit consequuntur 
4 quas a commodi doloremque omnis iure nisi.
*/
```

#### How to create a writable stream

- To write data to a writable stream you need to call `write()` on the stream instance. Like in the following example:

```javascript
readableStream.on('end', function () {
// console.log(data);
let writeStream = fs.createWriteStream('file2.txt');
writeStream.write(data);
});
```

