---
id: ami
title: AWS AMI
---

# Deploying ToolJet on Amazon AMI

:::warning
To enable ToolJet AI features in your ToolJet deployment, whitelist https://api-gateway.tooljet.ai.
:::

:::info
You should setup a PostgreSQL database manually to be used by ToolJet. We recommend using an **RDS PostgreSQL database**. You can find the system requirements [here](/docs/setup/system-requirements#postgresql).
:::

You can effortlessly deploy Amazon Elastic Compute Cloud Service (EC2) by utilizing a **CloudFormation template**. This template will deploy all the services required to run ToolJet on AWS AMI instances.

To deploy all the services at once, simply employ the following template:

```
curl -LO https://tooljet-deployments.s3.us-west-1.amazonaws.com/cloudformation/EC2-cloudfomration.yml
```

Follow the steps below to deploy ToolJet on AWS AMI instances.

1. Setup a PostgreSQL database and make sure that the database is accessible from the EC2 instance.
2. Login to your AWS management console and go to the EC2 management page.
3. Under the **Images** section, click on the **AMIs** button.
4. Find the [ToolJet version](/docs/setup/choose-your-tooljet) you want to deploy. Now, from the AMI search page, select the search type as "Public Images" and input the version you'd want `AMI Name : tooljet_vX.X.X.ubuntu_bionic` in the search bar.
5. Select ToolJet's AMI and bootup an EC2 instance. <br/>
   Creating a new security group is recommended. For example, if the installation should receive traffic from the internet, the inbound rules of the security group should look like this:

   | protocol | port | allowed_cidr |
   | -------- | ---- | ------------ |
   | tcp      | 22   | your IP      |
   | tcp      | 80   | 0.0.0.0/0    |
   | tcp      | 443  | 0.0.0.0/0    |

6. Once the instance boots up, SSH into the instance by running `ssh -i <path_to_pem_file> ubuntu@<public_ip_of_the_instance>`.

7. Switch to the app directory by running `cd ~/app`. <br/> Modify the contents of the `.env` file. ( Eg: `vim .env` )

   The default `.env` file looks like this:

   ```bash
   LOCKBOX_MASTER_KEY=
   SECRET_KEY_BASE=
   PG_DB=
   PG_USER=
   PG_HOST=
   PG_PASS=
   TOOLJET_DB=
   TOOLJET_DB_HOST=
   TOOLJET_DB_USER=
   TOOLJET_DB_PASS=
   ```

   Read **[environment variables reference](/docs/setup/env-vars)**

   :::info
   If there are self signed HTTPS endpoints that ToolJet needs to connect to, please make sure that `NODE_EXTRA_CA_CERTS` environment variable is set to the absolute path containing the certificates.
   :::

8. `TOOLJET_DB_HOST` environment variable determines where you can access the ToolJet client. It can either be the public ipv4 address of your instance or a custom domain that you want to use.

   Examples:
   `TOOLJET_DB_HOST=http://12.34.56.78` or
   `TOOLJET_DB_HOST=https://yourdomain.com` or
   `TOOLJET_DB_HOST=https://tooljet.yourdomain.com`

   :::info
   We use a [lets encrypt](https://letsencrypt.org/) plugin on top of nginx to create TLS certificates on the fly.
   :::

   :::info
   Please make sure that `TOOLJET_DB_HOST` starts with either `http://` or `https://`
   :::

9. Once you've configured the `.env` file, run `./setup_app`. This script will install all the dependencies of ToolJet and then will start the required services.
10. If you've set a custom domain for `TOOLJET_HOST`, add a `A record` entry in your DNS settings to point to the IP address of the EC2 instance.
11. You're all done, ToolJet client would now be served at the value you've set in `TOOLJET_HOST`.

#### Deploying ToolJet Database

ToolJet AMI comes inbuilt with PostgREST. If you intend to use this feature, you'd only have to setup the environment variables in `~/app/.env` file and run `./setup_app` script.

You can learn more about this feature [here](/docs/tooljet-db/tooljet-database).

## Upgrading to the Latest LTS Version

:::note
Users on versions earlier than **v2.23.0-ee2.10.2** must first upgrade to this version before proceeding to the LTS version.
:::

New LTS versions are released every 3-5 months with an end-of-life of atleast 18 months. To check the latest LTS version, visit the [ToolJet Docker Hub](https://hub.docker.com/r/tooljet/tooljet/tags) page. The LTS tags follow a naming convention with the prefix `LTS-` followed by the version number, for example `tooljet/tooljet:ee-lts-latest`.

Since ToolJet is deployed using an AMI (Amazon Machine Image), upgrading to a new LTS version requires launching a new EC2 instance with the updated AMI instead of upgrading in place.

#### Steps to Upgrade:

**1. Backup Your Data**

- Perform a comprehensive backup of your PostgreSQL database to prevent data loss.

**2. Copy the .env File from the old Instance**

- Before stopping the old instance, copy the `.env` file and store it safely.

**3. Stop the old EC2 Instance**

- To prevent conflicts, stop the old EC2 instance before proceeding with the new deployment.
- Ensure that the old instance remains stopped while setting up the new one.

**4. Launch a New EC2 Instance with the Latest AMI**

- Go to the AWS AMI dashboard and find the latest ToolJet AMI.
- Launch a new EC2 instance using this AMI.
- Configure security group rules as needed.

**5. Transfer the .env File to the New Instance**

- Upload the saved `.env` file to the appropriate directory on the new instance.

**6. Start the Application**

- SSH into the new instance, navigate to the app directory, and run the setup script:

  ```bash
  cd ~/app
  ./setup_app
  ```

**7. Terminate the Old EC2 Instance**

- After verifying that ToolJet is running correctly on the new instance, terminate the old EC2 instance to avoid unnecessary costs.

_If you have any questions feel free to join our [Slack Community](https://join.slack.com/t/tooljet/shared_invite/zt-2rk4w42t0-ZV_KJcWU9VL1BBEjnSHLCA) or send us an email at hello@tooljet.com._
