---
id: client
title: Deploying ToolJet client
---

# Deploying ToolJet client

ToolJet client is a standalone application and can be deployed on static website hosting services such as Netlify, Firebase, S3/Cloudfront, etc.

You can build standalone client with the below command:

```bash
SERVE_CLIENT=false npm run build
```

_If you have any questions feel free to join our [Slack Community](https://join.slack.com/t/tooljet/shared_invite/zt-2rk4w42t0-ZV_KJcWU9VL1BBEjnSHLCA) or send us an email at hello@tooljet.com._

## Deploying ToolJet client on Firebase

:::tip
You should set the environment variable `TOOLJET_SERVER_URL` ( URL of the server ) while building the frontend and also set `SERVE_CLIENT` to `false`` for standalone client build.

For example: `SERVE_CLIENT=false TOOLJET_SERVER_URL=https://server.tooljet.com npm run build && firebase deploy`
:::

1. Initialize firebase project
   ```bash
    firebase init
   ```
   Select Firebase Hosting and set build as the static file directory
2. Deploy client to Firebase
   ```bash
    firebase deploy
   ```

## Deploying ToolJet client with Google Cloud Storage

:::tip
You should set the environment variable `TOOLJET_SERVER_URL` ( URL of the server ) while building the frontend.

For example: `SERVE_CLIENT=false TOOLJET_SERVER_URL=https://server.tooljet.io npm run build`
:::

#### Using Load balancer

ToolJet client can be hosted from Cloud Storage bucket just like hosting any other static website.
Follow the instructions from google documentation [here](https://cloud.google.com/storage/docs/hosting-static-website).

Summarizing the steps below:

1. Create a bucket and upload files within the build folder such that the `index.html` is at the bucket root.

2. Edit permissions for the bucket to assign _New principal_ as `allUsers` with role as `Storage Object Viewer` and permit for public access for the bucket.

3. Click on _Edit website configuration_ from the [buckets browser](https://console.cloud.google.com/storage/browser?_ga=2.180838119.1530169400.1637242882-657891227.1637242882) and specify the main page as `index.html`

4. Follow the [instructions](https://cloud.google.com/storage/docs/hosting-static-website#lb-ssl) on creating a load balancer for hosting a static website.

5. Optionally, create Cloud CDN to use with the backend bucket assigned to the load balancer.

6. After the load balancer is created there will be an IP assigned to it. Try hitting it to check the website is being loaded.

7. Use the load balancer IP as the static IP for the A record of your domain.

#### Using Google App Engine

1. Upload the build folder onto a bucket

2. Upload `app.yaml` file onto bucket with the following config

   ```yaml
   runtime: python27
   api_version: 1
   threadsafe: true

   handlers:
     - url: /
       static_files: build/index.html
       upload: build/index.html

     - url: /(.*)
       static_files: build/\1
       upload: build/(.*)
   ```

3. Activate cloud shell on your browser and create build folder

   ```bash
   mkdir tooljet-assets
   ```

4. Copy the uploaded files onto an assets folder which is to be served

   ```bash
   gsutil rsync -r gs://your-bucket-name/path-to-assets ./tooljet-assets
   ```

5. Deploy static assets to be served
   ```bash
   cd tooljet-assets && gcloud app deploy
   ```

## Upgrading to the Latest LTS Version

New LTS versions are released every 3-5 months with an end-of-life of atleast 18 months. To check the latest LTS version, visit the [ToolJet Docker Hub](https://hub.docker.com/r/tooljet/tooljet/tags) page. The LTS tags follow a naming convention with the prefix `LTS-` followed by the version number, for example `tooljet/tooljet:ee-lts-latest`.

If this is a new installation of the application, you may start directly with the latest version. This guide is not required for new installations.

#### Prerequisites for Upgrading to the Latest LTS Version:

- It is crucial to perform a **comprehensive backup of your database** before starting the upgrade process to prevent data loss.

- Users on versions earlier than **v2.23.0-ee2.10.2** must first upgrade to this version before proceeding to the LTS version.

For specific issues or questions, refer to our **[Slack](https://join.slack.com/t/tooljet/shared_invite/zt-2rk4w42t0-ZV_KJcWU9VL1BBEjnSHLCA)**.
