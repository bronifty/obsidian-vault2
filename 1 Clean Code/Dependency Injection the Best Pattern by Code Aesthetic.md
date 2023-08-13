
rather than providing a bunch of optional properties to pass in and switch over, let's use an interface that represents our attachment storage, which contains an upload method which does what the request handler wants to do. 

```ts
export interface Storage {
	/**
	* Store an attachment
	* @param attachment The attachment to store
	* @return the attachment id
	*/
	upload(attachment: Attachment): Promise<string>;
	
	/**
	* Retrieve the attachment from the storage server 
	* @param attachment_id the attachment id
	**/
	download(attachment_id: string): Promise<Attachment>;
}
```

- then we create the implementations of the interface for each scenario; here is the AWS one; there is also SFTP and other one
```ts
export class AwsStorage implements Storage {
	public constructor(encryption: Encryption, aws_access_key_id: string, aws_secret_access_key: string) {
		this.encryption = encryption;
		AWS.config.credentials = new AWS.Credentials(aws_access_key_id, aws_secret_access_key);
		this.s3 = new AWS.S3({apiVersion: '2006-03-01'});
	}

	async upload(attachment: Attachment): Promise<string> {
		const local_path = await this.encryption.encrypt(attachment.user_id, attachment.local_path);
		let preview_local_path = null;
		if (attachment.preview_local_path) {
			preview_local_path = await this.encryption.encrypt(attachment.user_id, attachment.local_path)
		}

		const attachment_id = uuidv4();
		await this.upload_to_s3(attachment_id, attachment.message_id, local_path, preview_local_path);
		return attachment_id;
	}

	async download(attachment_id: string): Promise<Attachment> {
		const attachment = await this.fetch_attachment_s3(attachment_id);
		attachment.local_path = await this.encryption.decrypt(attachment.user_id, attachment.local_path);
		if (attachment.preview_local_path) {
			attachment.preview_local_path = await this.encryption.decrypt(attachment.user_id, attachment.preview_local_path);
		}
		return attachment;
	}
}
```

- once user is authd, we know which company they're from and which storage type they need;
- we create the storage and instead of passing the whole config to the request handler, we just pass in the storage as an argument to the request handler function
- the request handler doesn't know which storage is passed in or where the file is going, it just knows to call upload on the storage
##### Aside: strategy pattern is a concrete implementation of dependency injection 
- to implement this pattern, we'll use a factory, which is a class that uses conditionals to determine which class to use for instantiating the object which is returned
- storage factory is created by passing in the company id, upload object (which implements the storage interface, with upload method) is created by passing in the storage object, then the upload object's upload method is called, whose implementation is based on the storage object's upload method 
```ts
// storage factory is a class that creates AWS of SFTP storage based on the company id
const storage = storage_factory(company_id);
// storage has an upload method because it implements the Storage interface
const upload = new UploadRequest(storage);
// upload method is called on the object returned by passing in the storage object
const upload_id = await upload.upload(file);
// we have a derivative object or an object inception because one object is created in order to create another (storage factory creates storage and storage is used to create upload) 
return upload_id;
// finally upload method is called on upload object (an object created from the storage object which has a specific upload method based on the company id) and its return value is sent to the client
```








```ts
const storage = storage_factory.create_storage(req.company_id);

export class StorageFactory {

private readonly config: StorageConfiguration;

private readonly encryption: Encryption;

constructor (config: StorageConfiguration, encryption: Encryption) {

this.encryption = encryption;

this.config = config;

}

  

create_storage(company_id: string): Storage {

if (this.config.sftp_companies[company_id]) {

const config = this.config.sftp_companies[company_id];

return new SftpStorage(config.host, config.port, config.username, config.private_key);

}

  

if (this.config.webdav_companies[company_id]) {

const config = this.config.webdav_companies[company_id];

return new WebDavStorage(config.uri, config.authorization_key);

}

  

return new AwsStorage(this.encryption, this.config.aws_access_key_id, this.config.aws_secret_access_key)

}

}
```


```ts
export interface PreviewGenerator {
	/**
	* Makes a preview image for the given file
	*
	* @param filename The file to create a thumbail for
	* @return A promise that resolves to the thumbnail
	*/
	generate(filename: string): Promise<string>;
}
```


### Implement an Interface
- example interface 
```ts
// src/preview_generator.ts
export interface PreviewGenerator {
	/**
	* Makes a preview image from the file
	*
	* @param filename The file to create a thumbnail for
	* @return A promise which results to the image generated
	*/
	generate(filename: string): Promise<string>;
}
```
- example implementation
```ts
// src/previews/image_preview.ts
import { ImageScaler } from '../image_scaler';
import { PreviewGenerator } from '../preview_generator'

export class ImagePreviewGenerator implements PreviewGenerator {
	private readonly scaler: ImageScaler;
	private readonly MAX_WIDTH_PX = 250;
	
	constructor(scaler: ImageScaler) {
		this.scaler = scaler;
	}
	
	generate(filename: string): Promise<string> {
		return this.scaler.scale(filename, this.MAX_WIDTH_PX);
	}
}
```
- example registration
```ts
// factory will handle control flow (loops conditions and branching) to determine which implementation to create based on the parameter[s]
const preview_factory = new PreviewGeneratorFactory();
// 
const document_generator = new DocumentPreviewGenerator();

```

- reference: preview_factory
```ts
export class PreviewGeneratorFactory {
	private readonly generators = new Map<string, PreviewGenerator>();

	register(mime_type: string, generator: PreviewGenerator) {
		this.generators.set(mime_type, generator);
	}

	generate(mime_type: string) : PreviewGenerator | undefined {
		return this.generators.get(mime_type);
	}
}
```


```ts
// express app post route task
app.post(
	...params, () => {
		const upload = new UploadRequest(...params);
		upload.upload(attachment, mime_type)
	}
)
```

- the UploadRequest class takes a PreviewGeneratorFactory
```ts

```



The UploadRequest class constructed in the app's post route handler task (express app.post route) takes a preview factory... 

```ts
app.post(
	// file upload location
	"/attachment/upload",
	// callback
	async (req: Request, res: Response) => {
		const file = req.file;
		try {
			const upload = new UploadRequest(preview_factory);
			const attachment_id = await upload.upload(attachment, file.mimetype);
			res.send({ attachment_id: attachment_id });
		} catch (error: any) {
			console.error(error);
			res.status(400).send({ error: error.toString() });
		}
	}
);
```

the UploadRequest generates a preview from the factory passed in and uploads to the storage
```ts
export class UploadRequest {
	private readonly preview_factory: PreviewGeneratorFactory;
	constructor(preview: PreviewGeneratorFactory) {
		
	}
	const preview_generator = this.preview_factory.generate(mime_type);
	async upload()
}
```

the preview factory is a Map (with getter and setter) of mime type properties and Media Type PreviewGenerators 
```ts
export class PreviewGeneratorFactory {
	// generators = {
	//   "image/jpg": image_generator,
	//   "video/mp4": video_generator,
	//   "audio/mpeg": audio_generator,
	//   "application/pdf": document_generator,
	// }
	private readonly generators = new Map<string, PreviewGenerator>();

	register(mime_type: string, generator: PreviewGenerator) {
		this.generators.set(mime_type, generator);
	}

	generate(mime_type: string) : PreviewGenerator | undefined {
		return this.generators.get(mime_type);
	}
}
```

the Media Type PreviewGenerators are Document Image Movie and Audio; here is the ImagePreviewGenerator implementation as a reference
```ts
import { ImageScaler } from '../image_scaler';
import { PreviewGenerator } from '../preview_generator'

export class ImagePreviewGenerator implements PreviewGenerator {
	private readonly scaler: ImageScaler;
	private readonly MAX_WIDTH_PX = 250;
	
	constructor(scaler: ImageScaler) {
		this.scaler = scaler;
	}

	generate(filename: string): Promise<string> {
		return this.scaler.scale(filename, this.MAX_WIDTH_PX);
	}
}
```


1) the app will create the PreviewGeneratorFactory which has a no-arg constructor
	1) This is the registry with the mappings of mime types to preview generator types
	2) the app will register (set) and generate (get) the generators based on the mime type mappings
2) the app will create the PreviewGenerator type (eg ImagePreviewGenerator) with its necessary dependencies injected (scaler to scale the image down to thumbnail in order to generate the preview)
3) the app will use the factory to register the mime type with the PreviewGenerator type, so that the Factory is now the repository for all mime type to Concrete Implementation, which is essentially a data dictionary (maps the business term - mime type to the physical schema - the concrete implementation)
