```javascript
import dotenv from "dotenv";
dotenv.config();
import fs from "fs";
import minio from "minio";
// AWS_ENDPOINT = "s3.wasabisys.com";
// AWS_ACCESS_KEY_ID = "aws";
// AWS_SECRET_ACCESS_KEY = "aws";
// AWS_BUCKET_NAME = "bronifty-apps";
// YOUTUBE_API_KEY = "youtube";
const client = new minio.Client({
endPoint: process.env.AWS_ENDPOINT,
accessKey: process.env.AWS_ACCESS_KEY_ID,
secretKey: process.env.AWS_SECRET_ACCESS_KEY,
});

const file = fs.createReadStream("./temp/file2.txt");
client.putObject(
process.env.AWS_BUCKET_NAME,
"file2.txt",
file,
(err, etag) => {
if (err) {
console.log(err);
return;
}
console.log(`File uploaded successfully with etag ${etag}`);
}
);
```

