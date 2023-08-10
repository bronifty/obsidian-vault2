
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

- then we create the implementations of the interface for each scenario
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