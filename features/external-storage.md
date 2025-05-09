# External storage

External storage allows to use external servers for storing user uploads, which helps to leverage your server load and deliver a more reliable website. If you use multiple external storage servers, it will help to distribute the traffic of these assets.

::: tip 💡 Asset storage
Check the documentation on [Asset variables](https://v4-docs.chevereto.com/application/configuration/environment.html#assets-variables) if you want to control where to store the website assets (backgrounds, avatars, etc.)
:::

## How it works?

External storage works by adding external storage server(s) where file uploads will be stored. These external storage servers expose those files using HTTP, enabling users and visitors of your Chevereto installation to access these images directly.

## Storage URL

💡 The storage server must provide the URL for public-read file access. Check the documentation of your service provider.

Chevereto maps external storage uploads to the corresponding external storage server using the given Storage URL as a base URL to locate that file in the external storage.

Using Amazon S3 with direct storage:

| Property     | Value                                        |
| ------------ | -------------------------------------------- |
| Bucket       | my-bucket                                    |
| Storage URL  | https://s3.amazonaws.com/my-bucket/          |
| Stored image | my-bucket/image.jpg                          |
| Mapped URL   | https://s3.amazonaws.com/my-bucket/image.jpg |

::: warning CNAME
Is recommended that you use URLs that match your domain so try to take advantage of using a [CNAME record](https://en.wikipedia.org/wiki/CNAME_record).
:::

Amazon S3 with folder-based storage and custom CNAME (`img.domain.com`):

| Property     | Value                                                 |
| ------------ | ----------------------------------------------------- |
| Bucket       | my-bucket                                             |
| Storage URL  | https://img.domain.com/my-bucket/                     |
| Stored image | /my-bucket/2020/10/06/image.jpg                       |
| Mapped URL   | https://img.domain.com/my-bucket/2020/10/06/image.jpg |

## Storage URL with CDN

Add a CDN for each storage URL you want to use. At your CDN provider create a pull zone for the origin storage URL.

If you are using Amazon S3, the source (origin) URL will be something like this:

```sh
https://s3.amazonaws.com/my-bucket/
```

The CDN URL provided by your CDN service will be something like this:

```sh
https://pullzone-url.at.cdn-service.com/
```

Adding a CNAME record for the above URL will allow you to end up with a Storage URL like this:

```sh
https://s3-cdn.domain.com/
```

## Lifecycle settings

::: danger
Providers of cloud storage may use a feature called "lifecycle" for the files stored there. Make sure to edit the storage provider configuration to don't use this feature.
:::

When enabled, the storage provider will keep copies of all the states of the files (modified, deleted, etc.) which may seriously increase your storage costs. We highly recommend to always disable this kind of settings, when asked select to **keep only the last version of the file**.

## Providers

### Alibaba Cloud OSS

The Alibaba Cloud OSS API allows to upload images to [Alibaba Cloud (Aliyun) Object Storage System (OSS)](https://www.alibabacloud.com/product/oss/).

### Amazon S3

The Amazon S3 API allows to upload images to an [Amazon S3](https://en.wikipedia.org/wiki/Amazon_S3) bucket. You will need an [Amazon Web Services](https://aws.amazon.com/) (AWS) account for this.

To setup Amazon S3:

- Create access credentials from [Identity and Access Management](https://console.aws.amazon.com/iam/home?#users) console
  - Click on **Create users** and create a new user
  - On permissions, click on **Add permissions** and select **Attach policies directly** and associate **AmazonS3FullAccess**
  - Click on **Create access key** and proceed to create the access key for **Third-party service**
  - Store the **user name**, **Access Key** and **Secret access key** at the end of the process
- Create a bucket from the [S3 console](https://console.aws.amazon.com/s3)
  - Click on **Create bucket** and select **AWS Region** and **Bucket name** (store these)
  - On Object Ownership, make sure to select **ACLs enabled** and select **Bucket owner preferred** for the access control list
  - On Block Public Access make sure to leave *unchecked* **Block all public access** and make sure objects have public access
  - On Bucket Versioning, make sure to *disable* **Bucket Versioning**

If you want to use a custom domain follow the [CNAME](https://docs.aws.amazon.com/AmazonS3/latest/dev/VirtualHosting.html#VirtualHostingCustomURLs) documentation. Otherwise just make sure that the [Storage URL](#storage-url) ends with `/<your_bucket_name>/`

### Backblaze B2

The Backblaze B2 API allows to upload images to [Backblaze's cloud storage system](https://www.backblaze.com/b2/cloud-storage.html).

1. Go to **B2 Cloud Storage** and click on **Create a Bucket**
2. Files in Bucket are: **Public**
3. Go to **App Keys** and click on **Add a New Application Key**
   1. Type of Access: **Read and Write**
4. When done, use the following reference:

Select **S3 Compatible** storage API for **B2 S3 Storage** (current offering):

| B2 Value       | Chevereto Storage                              |
| -------------- | ---------------------------------------------- |
| Region         | `us-west-002` (take note from your Endpoint)   |
| keyID          | Storage key                                    |
| applicationKey | Storage secret                                 |
| Endpoint*      | https://s3.us-west-002.backblazeb2.com         |
| URL            | https://f002.backblazeb2.com/file/your_bucket/ |

> (*) You will find the endpoint under the bucket details.

Select **Backblaze B2** storage API for legacy **B2 Storage**:

| B2 Value       | Chevereto Storage                       |
| -------------- | --------------------------------------- |
| keyID          | Storage key (Account ID)                |
| applicationKey | Storage secret (Master Application Key) |

### FTP

The FTP API allows to upload images to a server implementing the [File Transfer Protocol](https://en.wikipedia.org/wiki/File_Transfer_Protocol).

### Google Cloud

The Google Cloud API allows to upload images to a Google Cloud Storage bucket. You will need a [Google Cloud](https://cloud.google.com/) service account and [activate cloud storage](https://cloud.google.com/storage/docs/signup) for this.

To setup Google Cloud Storage:

- Create a project
- Go to **APIs & services** dashboard and make sure that **Google Cloud Storage JSON API** is enabled.
  - If is not enabled click on **Enable API and Services** and enable Google Cloud Storage JSON API
- Go to **Cloud Storage** then click on **Browser**
- Create a bucket by clicking **Create bucket** button. Make sure to:
  - Prevent public access: Unselect **Enforce public access prevention on this bucket** as you want public access for the bucket
  - Access control: Fine-grained
- Go to **Credentials** under APIs & services, click on **Create credentials** then click on **Service account**. Make sure to use the following settings:
  - Grant access: Role owner
  - Key type: JSON
- When done, go to your newly created service account under **Service Accounts**
- Go yo **keys** and create a new **JSON key**
- Your browser will start to download the JSON key file, the contents of the file is what you need to paste on Chevereto's **Secret Key** textarea

### Local

The [Local API](../settings/external-storage.md#local) allows to upload images to any filesystem path in the server.

### Microsoft Azure

The Microsoft Azure API allows to upload images to [Microsoft Azure Storage](https://azure.microsoft.com/services/storage/).

### OpenStack

The [OpenStack API](../settings/external-storage.md#openstack) allows to upload images to an [OpenStack](https://en.wikipedia.org/wiki/OpenStack) container.

- OpenStack configuration for RunAbove:
  - Identity URL: <https://auth.Runabove.io/v2.0>
  - Username: Your RunAbove username
  - Password: Your RunAbove password
  - Region: `SBG-1` or `BHS-1` This is the data center where your container was created
  - Container: Name of your created container
  - Tenant id: Leave it blank
  - Tenant name: Your `project id`, found on OpenStack Horizon on the left side (CURRENT PROJECT))
  - URL: Your URL to access the container (see [RunAbove CNAME](https://community.runabove.com/kb/en/object-storage/how-to-put-object-storage-behind-your-domain-name.html))

### S3 Compatible

The S3 Compatible API allows to upload images to any server implementing the Amazon S3 standard, also known as "AWS S3 API". The configuration is exactly the same as Amazon S3, but it requires to provide the provider endpoint.

Some providers supporting S3 API are:

- Backblaze B2 (via S3-compatible API)
- Cloudflare R2
- DigitalOcean Spaces
- DreamHost DreamObjects
- Hetzner Storage Boxes (via MinIO, partial S3 support)
- IBM COS S3
- IDrive® e2
- Linode Object Storage (now Akamai)
- OVH Cloud Object Storage
- PhoenixNAP Object Storage
- Scaleway Object Storage
- StackPath
- Storj (S3 Gateway access via third-party or hosted integration)
- Tencent Cloud Object Storage (COS)
- Vultr Object Storage (use region `us-east-1`)
- Wasabi

Self-hosted S3-compatible solutions:

- MinIO
- Ceph (RGW)
- SeaweedFS (S3 Gateway)
- Zenko (by Scality)
- LeoFS (S3 support in gateway mode)
- Garage (Deuxfleurs)

### SFTP

The SFTP API allows to upload images to a server implementing the [SSH File Transfer Protocol](https://en.wikipedia.org/wiki/SSH_File_Transfer_Protocol).
