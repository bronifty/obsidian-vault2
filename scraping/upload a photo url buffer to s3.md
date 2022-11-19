- [YouTube tut](https://www.youtube.com/watch?v=DtuL1ALeD2c)

```shell
# .env
UPLOAD_URL=http://i3.ytimg.com/vi/J---aiyznGQ/mqdefault.jpg
AWS_BUCKET_NAME=bronifty
AWS_BUCKET_REGION=us-east-1
AWS_ACCESS_KEY=accesskey
AWS_SECRET_ACCESS_KEY=secretkey
```

```js
import http from "http";
import { S3Client, PutObjectCommand } from "@aws-sdk/client-s3";
import dotenv from "dotenv";
dotenv.config();
const uploadURL = process.env.UPLOAD_URL;
const region = process.env.AWS_BUCKET_REGION;
const accessKeyId = process.env.AWS_ACCESS_KEY;
const secretAccessKey = process.env.AWS_SECRET_ACCESS_KEY;
const bucket = process.env.AWS_BUCKET_NAME;
const s3Client = new S3Client({
	region,
	credentials: {
		accessKeyId,
		secretAccessKey,
	},
});

export function uploadFile(fileBuffer, fileName, mimetype) {
	const uploadParams = {
		Bucket: bucket,
		Body: fileBuffer,
		Key: fileName,
		ContentType: mimetype,
	};
	return s3Client.send(new PutObjectCommand(uploadParams));
}

http.get(uploadURL, function (res) {
	var data = [];
	res
		.on("data", function (chunk) {
			data.push(chunk);
		})
		.on("end", function () {
			let buffer = Buffer.concat(data);
			console.log(buffer.toString("base64"));
			uploadFile(buffer, "file.jpg", "image/jpg");
		});
});
```



- [flavio notes for web fetch url to buffer](https://flaviocopes.com/node-aws-s3-upload-image/)
```js
const imageURL = 'https://url-to-image.jpg'
const res = await fetch(imageURL)
const blob = await res.buffer()
```

