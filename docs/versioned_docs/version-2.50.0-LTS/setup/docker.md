---
id: docker
title: Docker
---

import Tabs from '@theme/Tabs';
import TabItem from '@theme/TabItem';

# Deploying ToolJet using Docker Compose

Follow the steps below to deploy ToolJet on a server using Docker Compose. ToolJet requires a PostgreSQL database to store applications definitions, (encrypted) credentials for datasources and user authentication data.

:::info
If you rather want to try out ToolJet on your local machine with Docker, you can follow the steps [here](/docs/setup/try-tooljet/).
:::

### Installing Docker and Docker Compose

Install docker and docker-compose on the server.

- Docs for [Docker Installation](https://docs.docker.com/engine/install/)
- Docs for [Docker Compose Installation](https://docs.docker.com/compose/install/)

### Deployment options

There are two options to deploy ToolJet using Docker Compose:

1. **With in-built PostgreSQL database (recommended)**. This setup uses the official Docker image of PostgreSQL.
2. **With external PostgreSQL database**. This setup is recommended if you want to use a managed PostgreSQL service such as AWS RDS or Google Cloud SQL.

Confused about which setup to select? Feel free to ask the community via [Slack](https://join.slack.com/t/tooljet/shared_invite/zt-2rk4w42t0-ZV_KJcWU9VL1BBEjnSHLCA).

<Tabs>
  <TabItem value="with-in-built-postgres" label="With in-built PostgreSQL" default>

1. Download our production docker-compose file into the server.

```bash
curl -LO https://tooljet-deployments.s3.us-west-1.amazonaws.com/docker/docker-compose-db.yaml
mv docker-compose-db.yaml docker-compose.yaml
mkdir postgres_data
```

2. Create `.env` file in the current directory (where the docker-compose.yaml file is downloaded as in step 1):

```bash
curl -LO https://tooljet-deployments.s3.us-west-1.amazonaws.com/docker/.env.internal.example
curl -LO https://tooljet-deployments.s3.us-west-1.amazonaws.com/docker/internal.sh && chmod +x internal.sh
mv .env.internal.example .env && ./internal.sh
```

`internal.sh` helps to generate the basic .env variables such as the LOCKBOX_MASTER_KEY, SECRET_KEY_BASE, and the password for postgreSQL database.

3. To start the docker container, use the following command:

```bash
docker-compose up -d
```

4. **(Optional)** `TOOLJET_HOST` environment variable can either be the public ipv4 address of your server or a custom domain that you want to use. Which can be modified in the .env file.

Also, for setting up additional environment variables in the .env file, please check our documentation on [environment variable](/docs/setup/env-vars)

Examples:
`TOOLJET_HOST=http://12.34.56.78` or
`TOOLJET_HOST=https://tooljet.yourdomain.com`

If you've set a custom domain for `TOOLJET_HOST`, add a `A record` entry in your DNS settings to point to the IP address of the server.

:::info
i. Please make sure that `TOOLJET_HOST` starts with either `http://` or `https://`

ii. Setup docker to run without root privileges by following the instructions written here https://docs.docker.com/engine/install/linux-postinstall/

iii. If you're running on a linux server, `docker` might need sudo permissions. In that case you can either run:
`sudo docker-compose up -d`
:::

### Docker Backup (Only For In-Built PostgreSQL)

The below bash script will help with taking back-up and as well as restoring:

1. Download the script:

```bash
curl -LO https://tooljet-deployments.s3.us-west-1.amazonaws.com/docker/backup-restore.sh && chmod +x backup-restore.sh
```

2. Run the script with the following command:

```bash
./backup-restore.sh
```

<div style={{textAlign: 'center'}}>
  <img className="screenshot-full" src="/img/setup/docker/backup-and-restore.gif" alt="Docker - Backup and Restore" />
</div>

  </TabItem>
  <TabItem value="with-external-postgres" label="With external PostgreSQL">

1. Setup a PostgreSQL database and make sure that the database is accessible.

2. Download our production docker-compose file into the server.

```bash
curl -LO https://tooljet-deployments.s3.us-west-1.amazonaws.com/docker/docker-compose.yaml
```

3. Create `.env` file in the current directory (where the docker-compose.yaml file is downloaded as in step 1):

Kindly set the postgresql database credentials according to your external database. Please enter the database details with the help of the bash as shown below.

  <div style={{textAlign: 'center'}}>

  <img className="screenshot-full" src="/img/setup/docker/bash.gif"/>

  </div>

```bash
curl -LO https://tooljet-deployments.s3.us-west-1.amazonaws.com/docker/.env.external.example
curl -LO https://tooljet-deployments.s3.us-west-1.amazonaws.com/docker/external.sh && chmod +x external.sh
mv .env.external.example .env && ./external.sh
```

4. To start the docker container, use the following command:

```bash
docker-compose up -d
```

5. **(Optional)** `TOOLJET_HOST` environment variable can either be the public ipv4 address of your server or a custom domain that you want to use. Which can be modified in the .env file.

Also, for setting up additional environment variables in the .env file, please check our documentation on [environment variable](/docs/setup/env-vars)

Examples:
`TOOLJET_HOST=http://12.34.56.78` or
`TOOLJET_HOST=https://tooljet.yourdomain.com`

If you've set a custom domain for `TOOLJET_HOST`, add a `A record` entry in your DNS settings to point to the IP address of the server.

:::info
i. Please make sure that `TOOLJET_HOST` starts with either `http://` or `https://`

ii. If there are self signed HTTPS endpoints that ToolJet needs to connect to, please make sure that `NODE_EXTRA_CA_CERTS` environment variable is set to the absolute path containing the certificates.

iii. If you're running a linux server, `docker` might need sudo permissions. In that case you can either run:
`sudo docker-compose up -d`

iv. Setup docker to run without root privileges by following the instructions written here https://docs.docker.com/engine/install/linux-postinstall/
:::

</TabItem>
</Tabs>

## Upgrading to the Latest LTS Version

New LTS versions are released every 3-5 months with an end-of-life of atleast 18 months. To check the latest LTS version, visit the [ToolJet Docker Hub](https://hub.docker.com/r/tooljet/tooljet/tags) page. The LTS tags follow a naming convention with the prefix `LTS-` followed by the version number, for example `tooljet/tooljet:EE-LTS-latest`.

If this is a new installation of the application, you may start directly with the latest version. This guide is not required for new installations.

#### Prerequisites for Upgrading to the Latest LTS Version:

- It is crucial to perform a **comprehensive backup of your database** before starting the upgrade process to prevent data loss.

- Users on versions earlier than **v2.23.0-ee2.10.2** must first upgrade to this version before proceeding to the LTS version.

_If you have any questions feel free to join our [Slack Community](https://join.slack.com/t/tooljet/shared_invite/zt-2rk4w42t0-ZV_KJcWU9VL1BBEjnSHLCA) or send us an email at hello@tooljet.com._
