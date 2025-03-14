# Overview

This guide will walk you through the process of deploying a sample Node.js application in CFT.
An application will be generated from a template and deployed to a Kubernetes cluster.
At the end of this tutorial, you will be able to access your application via the HMCTS VPN and will have made changes to it.

## Prerequisites

Before starting this tutorial, make sure you have:

- An account on HMCTS Azure AD. See the [onboarding guide](https://hmcts.github.io/cloud-native-platform/onboarding/person/index.html#microsoft-entra-id-groups) to get set up.
- Access to the HMCTS GitHub organisation. See the [onboarding guide](https://hmcts.github.io/cloud-native-platform/onboarding/team/github.html#github) to get setup.
- (Recommended) Join the below Slack channels
  - [#golden-path](https://hmcts-reform.slack.com/app_redirect?channel=golden-path) is for community discussion about the tutorials.
  - [#labs-build-notices](https://hmcts-reform.slack.com/app_redirect?channel=labs-build-notices) Jenkins build notices channel.
  - [#platops-help](https://hmcts-reform.slack.com/app_redirect?channel=platops-help) is for raising support requests to the `Platform Operations` team.
 
## Troubleshooting

If you run into any issues while running through the steps below, please see our [troubleshooting](https://hmcts.github.io/cloud-native-platform/troubleshooting/#troubleshooting-issues) guide for information on how to resolve any issues you may encounter.

## Steps

### Create a repository

First, we're going to create a GitHub repository with our template. You'll need to fill in a few fields and select a name for the GitHub repository.

Click `Create` in the Backstage sidebar and choose [`ExpressJS`](https://backstage.platform.hmcts.net/create) template.

   Fill out the fields:

- Page 1 - Provide some simple information
  - Product:                       `labs`

  - Component:                     `YourGithubUsername-nodejs` - make sure you update this

  - Slack contact channel:         `cloud-native`

  - Description:                   `Deploying a Node.js application`

  - HTTP port:                     `8080` - do not use any port number lower than 1024

  - GitHub admin team:             `hmcts/all-org-members` - you can also use your own team

  - Owner:                         `dts_cft_developers` - normally you would use your teams AzureAD group here

- Page 2 - Choose a location
  - Host:                          `github.com`

  - Owner:                         `hmcts`

  - Repository:                    `labs-YourGithubUsername-nodejs`

### Build application

1. Log in to Sandbox Jenkins and select [HMCTS - J to Z](https://sandbox-build.platform.hmcts.net/job/HMCTS_j_to_z_Sandbox/) folder. Check if your repository is there, if it's not then scan the organization by clicking on `Scan Organization Now`.
The new repository should be listed under repositories after the scan finishes.
Logs can be monitored under `Scan Organization Log`.

2. Click on your repository name.

3. Wait for your build to complete against the `master` branch, (click the Play button if it hasn't started by itself).

### Configure Public DNS for Application

Configuring Public DNS is required by Azure Front Door to prove that we own the domain.
You will need to add a couple of lines of config for the application to the sandbox public DNS config file.

1. Open [environments/sandbox.yml](https://github.com/hmcts/azure-public-dns/blob/master/environments/sandbox/sandbox.yml) in your browser.
2. Click the pencil icon to edit the file in Github.
3. Add configuration to the bottom of the file, see [example](https://github.com/hmcts/azure-public-dns/pull/1829/files). You can also find the hostname of the Front Door by searching for `hmcts-sbox` in the Azure Portal, in case it has changed.
4. Scroll down to the bottom to the 'Commit changes' section. Select `Create a new branch for this commit and start a pull request` and give your branch a name. Commit the file and create a pull request.
5. To complete this section you will need your pull request to be approved, someone on your team should be able to do this. If you get stuck, try asking in #platops-code-review on Slack. Once approved and the checks have passed, merge your pull request. If you have a permissions issue, ask in the #golden-path Slack channel.
6. Once your pull request has been merged, ensure the pipeline has run successfully and applied your changes in [Azure Devops](https://dev.azure.com/hmcts/PlatformOperations/_build?definitionId=278&_a=summary).

Further reading on how we configure Public DNS can be found in [The HMCTS Way](https://hmcts.github.io/cloud-native-platform/path-to-live/public-dns.html#public-dns).

### Configure Private DNS for Application

Private DNS configuration allows for your application to be accessed over the VPN.
You will need to add a couple of lines of config for the application to the sandbox private DNS config file.

1. Open [sandbox-platform-hmcts-net.yml](https://github.com/hmcts/azure-private-dns/blob/master/environments/sandbox/sandbox-platform-hmcts-net.yml) in your browser.
2. Click the pencil icon to edit the file in Github.
3. Add configuration to the bottom of the file, see [example](https://github.com/hmcts/azure-private-dns/pull/822/files).
4. Scroll down to the bottom to the 'Commit changes' section. Select `Create a new branch for this commit and start a pull request` and give your branch a name. Commit the file and create a pull request.
5. To complete this section you will need your pull request to be approved, someone on your team should be able to do this. If you get stuck try asking in #platops-code-review on Slack. Once approved and the build has passed then merge your pull request. If you have a permissions issue then ask in #golden-path on Slack.
6. Once your pull request has been merged, ensure the pipeline has run successfully and applied your changes in [Azure Devops](https://dev.azure.com/hmcts/PlatformOperations/_build?definitionId=775&_a=summary).

### Configure Front Door for Application

You will need to add a couple of lines of config for the application to the sandbox front door config file.

1. Open [sbox/sbox.tfvars](https://github.com/hmcts/azure-platform-terraform/blob/master/environments/sbox/sbox.tfvars) in your browser.
2. Click the pencil icon to edit the file in GitHub.
3. Add configuration to the bottom of the file, see [example](https://github.com/hmcts/azure-platform-terraform/pull/2229/files)
4. Scroll down to the bottom to the 'Commit changes' section. Select `Create a new branch for this commit and start a pull request` and give your branch a name. Commit the file and create a pull request.
5. To complete this section you will need your pull request to be approved, someone on your team should be able to do this. If you get stuck, try asking in #platops-code-review on Slack. Once approved and the build has passed then merge your pull request. If you have a permissions issue then ask in #golden-path on Slack.

More information on how we configure Azure Front Door can be found in [The HMCTS Way](https://hmcts.github.io/cloud-native-platform/path-to-live/front-door.html#purpose).

### Configure Azure Firewall for Application

1. Checkout the [hub-terraform-infra](https://github.com/hmcts/hub-terraform-infra) repo.
2. Navigate to the file [path](https://github.com/hmcts/hub-terraform-infra/blob/master/environments/hub_infra-sbox-int.tfvars).
3. Click the pencil icon to edit the file in GitHub.
4. Add the below snippet to the `public_lb_config` and remember to put a comma after the previous entry.

    ```
    {
      name  = "labs-YourGitHubUsername-nodejs"
      ports = [80]
    }
    ```

5. Add the below snippet to the `aks_config` and remember to put a comma after the previous entry and increment the index to the next available, otherwise the pipeline will fail on apply. You can get the IP of the frontend app gateway from [here](https://github.com/hmcts/azure-platform-terraform/blob/6f0b867e75b7e9cee9e7adc87084f6911eb5373d/environments/sbox/sbox.tfvars#L20).

    ```
    {
      name : "labs-YourGitHubUsername-nodejs",
      palo_ips : {
        "uksouth" : "<IP of the frontend app gateway>",
      },
      port : [80, ]
      index : 9
    }
    ```

6. Scroll down to the bottom to the 'Commit changes' section. Select `Create a new branch for this commit and start a pull request` and give your branch a name. Commit the file and create a pull request.
7. To complete this section you will need your pull request to be approved, someone on your team should be able to do this. If you get stuck, try asking in #platops-code-review on Slack. Once approved and the build has passed then merge your pull request. If you have a permissions issue then ask in #golden-path on Slack.

### Deploy application

We use [GitOps](https://docs.gitops.weave.works/) principles for application deployment to Kubernetes.

Your application will be deployed in the `labs` Kubernetes namespace which has already been created.

For the benefit of of this tutorial we have created a separate [guide](https://github.com/hmcts/cnp-flux-config/blob/master/labs/README.md#creating-the-flux-config-for-your-lab-application) to help you create the flux config needed to deploy your lab application with Flux and to enable Flux to automate updating image tags to the latest version of your image.
It's also worth taking a look at the app deployment [guide](https://github.com/hmcts/cnp-flux-config/blob/master/docs/app-deployment-v2.md#application) in the cnp-flux-config repo to understand how you would do this normally.

A couple of minutes after your pull request has been merged, you should see your HelmRelease resource deployed and your application's image tag updated to the latest version with the `prod-xxxxxxx-xxxxxxxxxxxxxx` naming convention. If this version number hasn't been updated, there may be issues in your Jenkins run from earlier (i.e. you should never have to manually add this version number on).

To confirm your change has been applied in the cluster, you can connect to the cluster by running the commands below:

```command
 az login
 az aks get-credentials --resource-group cft-sbox-00-rg --name cft-sbox-00-aks --subscription DCD-CFTAPPS-SBOX --overwrite-existing
```

To make sure your pod is running as expected and to check the status of your HelmRelease run the following commands (make sure to swap YourGithubUsername with your GitHub username):

```command
 kubectl get hr labs-YourGithubUsername-nodejs -n labs
 kubectl get pods -l app.kubernetes.io/name=labs-YourGithubUsername-nodejs -n labs
```

### Access application

If all went well, your application should be accessable now.

The URL will be (update the GitHub username variable):

   ```text
   http://labs-$YourGitHubUsername-nodejs.sandbox.platform.hmcts.net
   ```  

Open this in your browser, you should see:
![Default Page Template](images/DefaultPageTemplate.png)

### Customise application

We are going to update the application by changing the text on the home page.

1. Go to the Github repo you created at the beginning of this tutorial.
2. Open the file 'home.njk' under 'src/main/views'.
3. Edit the html heading to change the webpage's text.
4. Open the file 'smoke.ts' under 'src/test/smoke'.
5. Edit the smoke test to reference the change to the page heading.
6. Open the file 'hello-world.feature' under 'src/test/functional/features'.
7. Edit the functional test to reference the change to the page heading.
8. Ask someone in your team to review your pull request and then merge it.
9. Run the Jenkins pipeline against the master branch (this will trigger automatically on the production Jenkins instance).
10. Reload your application in your browser and check it now shows your change to the heading:
![Hello World](images/HelloWorld.png)

## Feedback

[comment]: <> (As of March 2022)
This is a new way of onboarding developers that we are trying out.
If you could provide feedback it would really help us improve it for others.
The [survey](https://forms.office.com/r/P2YbcLVAr4) will only take you a couple of minutes to complete.

If you've found a problem with the guide please [report an issue](https://github.com/hmcts/golden-path-nodejs/issues) instead, or you can create a pull request to correct it yourself.

If you need help with the lab please reach out in [#golden-path](https://hmcts-reform.slack.com/app_redirect?channel=golden-path).

If you notice an error in this documentation, please raise a pull request in the [GitHub repo](https://github.com/hmcts/golden-path-nodejs/blob/master/docs/index.md) with the necessary updates.
