## Overview
This guide will walk you through the process of deploying a sample Node.js application in CFT.
An application will be generated from a template and deployed to a Kubernetes cluster.
At the end of this tutorial, you will be able to access your application via the HMCTS VPN and will have made changes to it.

## Prerequisites
GitHub access is required to complete the steps in this tutorial. See the [onboarding guide](https://hmcts.github.io/onboarding/team/github.html#github) to get setup.

Join the [slack channels](https://github.com/hmcts/golden-path-nodejs/blob/master/docs/index.md#slack-channels)

## Slack Channels

- [#golden-path](https://hmcts-reform.slack.com/app_redirect?channel=golden-path) is for community discussion about the tutorials.
- [#labs-build-notices](https://hmcts-reform.slack.com/app_redirect?channel=labs-build-notices) Jenkins build notices channel.
- [#platops-help](https://hmcts-reform.slack.com/app_redirect?channel=platops-help) is for raising support requests to the `Platform Operations` team.

## Steps

#### Create a repository
First we are going to create a GitHub repository with our template, you'll need to fill in a few fields and select a name for the GitHub repository.

Click `Create` in the Backstage sidebar and select [`ExpressJS`](https://backstage.platform.hmcts.net/create) template. 

   Fill out the fields:
   
   
- Page 1 - Provide some simple information
   - Product:                       `labs`
      
   - Component:                     `YourGithubUsername` - make sure you update this

   - Slack contact channel:         `cloud-native`
      
   - Description:                   `Deploying a Node.js application`

   - HTTP port:                     `8080` - do not use any port number lower than 1024

   - GitHub admin team:             `hmcts/reform` - you can also use your own team

   - Owner:                         `dts_cft_developers` - normally you would use your teams AzureAD group here
    
- Page 2 - Choose a location
   - Host:                          `github.com`
   
   - Owner:                         `hmcts`
   
   - Repository:                    `labs-YourGithubUsername`

#### Build application

1. Log in to Sandbox Jenkins and select [HMCTS - Labs](https://sandbox-build.platform.hmcts.net/job/HMCTS_Sandbox_LABS/) folder. Check if your repository is there, if it's not then scan the organization by clicking on `Scan Organization Now`.
The new repository should be listed under repositories after the scan finishes.
Logs can be monitored under `Scan Organization Log`.
Any GitHub repository that starts with `labs-*` will be listed as part of this scan.

2. Click on your repository name.

3. Wait for your build to complete against the `master` branch, (click the Play button if it hasn't started by itself).

#### Configure Public DNS for Application
You will need to add a couple of lines of config for the application to the sandbox public DNS config file.
1. Open [environments/sandbox.yml](https://github.com/hmcts/azure-public-dns/blob/master/environments/sandbox.yml) in your browser.
2. Click the pencil button to edit the file in Github.
3. Add configuration to the bottom of the file, see example [here](https://github.com/hmcts/azure-public-dns/blob/27e92eb7881eafe81dd9fbed47c51c18630f862e/environments/sandbox.yml#L156)
4. Scroll down to the bottom to the 'Commit changes' section. Select create a new branch for this commit and give your branch a name. Commit the file and create a pull request.
5. To complete this section you will need your pull request to be approved, someone on your team should be able to do this. If you get stuck try asking in #platops-code-review on Slack. Once approved and the build has passed then merge your pull request. If you have a permissions issue then ask in #golden-path on Slack.
6. Once your pull request has been merged, ensure the pipeline has ran successfully and applied you changes [here](https://dev.azure.com/hmcts/PlatformOperations/_build?definitionId=278&_a=summary) before proceeding to the [next step](https://github.com/hmcts/golden-path-nodejs/blob/DTSPO-5507-DocumentingNodejsGoldenPath/docs/index.md#configure-front-door-for-application).

More info can be found [here](https://hmcts.github.io/ways-of-working/path-to-live/public-dns.html#public-dns). 

#### Configure Front Door for Application
You will need to add a couple of lines of config for the application to the sandbox front door config file.
1. Open [sbox/sbox.tfvars](https://github.com/hmcts/azure-platform-terraform/blob/master/environments/sbox/sbox.tfvars) in your browser.
2. Click the pencil button to edit the file in GitHub.
3. Add configuration to the bottom of the file, see example [here](https://github.com/hmcts/azure-platform-terraform/blob/acb0c544127d136c14f0f1d15ba24f79132a9b60/environments/sbox/sbox.tfvars#L396)
4. Scroll down to the bottom to the 'Commit changes' section. Select create a new branch for this commit and give your branch a name. Commit the file and create a pull request.
5. To complete this section you will need your pull request to be approved, someone on your team should be able to do this. If you get stuck try asking in #platops-code-review on Slack. Once approved and the build has passed then merge your pull request. If you have a permissions issue then ask in #golden-path on Slack.

More info can be found [here](https://hmcts.github.io/ways-of-working/path-to-live/front-door.html#purpose). 

#### Deploy application


#### Access application
If all went well your application should be visible now.

The URL will be (update the GitHub username variable):
   ```
   http://labs-$yourGitHubUsername.sandbox.platform.hmcts.net
   ```  

Open this in your browser, you should see:


#### Customise application
We are going to update the application by changing the home page.
1. Open the 'home.njk' file inside the 'src/main/views'
2. Edit the html heading to change the webpage's text
  ```yaml
  <h1 class="govuk-heading-xl">I've made my first change to a webpage</h1>
   ```
3. Ask someone on your team to review your pull request and then merge it.
4. Run the Jenkins pipeline against the master branch (this will trigger automatically on the production Jenkins instance).
6. Reload your application in your browser and check it now shows your change to the heading.

## Feedback
text here

## Troubleshooting

See our [troubleshooting](https://hmcts.github.io/ways-of-working/troubleshooting/#troubleshooting-issues) guide.
        