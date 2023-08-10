
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
