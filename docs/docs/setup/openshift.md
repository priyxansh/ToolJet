---
id: openshift
title: Openshift
---

# Deploying ToolJet on Openshift

:::info
You should setup a PostgreSQL database manually to be used by ToolJet.
:::

Follow the steps below to deploy ToolJet on Openshift.

1. Setup a PostgreSQL database ToolJet uses a postgres database as the persistent storage for storing data related to users and apps. We do not have plans to support other databases such as MySQL.

2. Create a Kubernetes secret with name `server`. For the setup, ToolJet requires:

- **TOOLJET_DB**
- **TOOLJET_DB_HOST**
- **TOOLJET_DB_USER**
- **TOOLJET_DB_PASS**
- **PG_HOST**
- **PG_DB**
- **PG_USER**
- **PG_PASS**
- **SECRET_KEY_BASE**
- **LOCKBOX_KEY**

Read **[environment variables reference](/docs/setup/env-vars)**

3. Once you have logged into the Openshift developer dashboard click on `+Add` tab. Select import YAML from the local machine.

:::note
When entering one or more files and use --- to separate each definition
:::

Copy paste deployment.yaml to the online editor

```
curl -LO https://tooljet-deployments.s3.us-west-1.amazonaws.com/pre-release/openshift/deployment.yaml
```

Copy paste the service.yaml to the online editor

```
curl -LO https://tooljet-deployments.s3.us-west-1.amazonaws.com/pre-release/openshift/service.yaml
```

<div style={{textAlign: 'center'}}>

<img className="screenshot-full" src="/img/setup/openshift/online-yaml-editor.png" alt="online yaml editor" />
 
</div>

Once you have added the files click on create.

:::info
If there are self signed HTTPS endpoints that Tooljet needs to connect to, please make sure that `NODE_EXTRA_CA_CERTS` environment variable is set to the absolute path containing the certificates. You can make use of kubernetes secrets to mount the certificate file onto the containers.
:::

4. Navigate to topology tab and use the visual connector to establish the connect between tooljet-deployment and postgresql as shown in the screenshot below.

<div style={{textAlign: 'center'}}>

<img className="screenshot-full" src="/img/setup/openshift/toplogy.png" alt="topology" />
 
</div>

## Workflows

ToolJet Workflows allows users to design and execute complex, data-centric automations using a visual, node-based interface. This feature enhances ToolJet's functionality beyond building secure internal tools, enabling developers to automate complex business processes.

Create workflow deployment:

```bash
kubectl apply -f https://tooljet-deployments.s3.us-west-1.amazonaws.com/pre-release/kubernetes/workflow-deployment.yaml
```

**Note:** Ensure that the worker deployment uses the same image as the ToolJet application deployment to maintain compatibility. Additionally, the variables below need to be a part of tooljet-deployment.

`ENABLE_WORKFLOW_SCHEDULING=true`
`TOOLJET_WORKFLOWS_TEMPORAL_NAMESPACE=default`
`TEMPORAL_SERVER_ADDRESS=<Temporal_Server_Address>`

## Upgrading to the Latest LTS Version

New LTS versions are released every 3-5 months with an end-of-life of atleast 18 months. To check the latest LTS version, visit the [ToolJet Docker Hub](https://hub.docker.com/r/tooljet/tooljet/tags) page. The LTS tags follow a naming convention with the prefix `LTS-` followed by the version number, for example `tooljet/tooljet:ee-lts-latest`.

If this is a new installation of the application, you may start directly with the latest version. This guide is not required for new installations.

#### Prerequisites for Upgrading to the Latest LTS Version:

- It is crucial to perform a **comprehensive backup of your database** before starting the upgrade process to prevent data loss.

- Users on versions earlier than **v2.23.0-ee2.10.2** must first upgrade to this version before proceeding to the LTS version.

_If you have any questions feel free to join our [Slack Community](https://join.slack.com/t/tooljet/shared_invite/zt-2rk4w42t0-ZV_KJcWU9VL1BBEjnSHLCA) or send us an email at hello@tooljet.com._
