## Overview
This guide will walk you through the process of deploying a sample Node.js application in CFT.
An application will be generated from a template and deployed to a Kubernetes cluster.
At the end of this tutorial, you will be able to access your application via the HMCTS VPN and will have made changes to it.

## Prerequisites
The following are required to complete the steps in this tutorial:
- Github Steps
- VPN Access

See the [onboarding guide](https://hmcts.github.io/onboarding/team/github.html#github) to get setup.

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

#### Configure load balancing for high availability
#### Deploy application
#### Access application

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
        

## Slack Channels

- [#golden-path](https://hmcts-reform.slack.com/app_redirect?channel=golden-path) is for community discussion about the tutorials.
- [#labs-build-notices](https://hmcts-reform.slack.com/app_redirect?channel=labs-build-notices) Jenkins build notices channel.
- [#platops-help](https://hmcts-reform.slack.com/app_redirect?channel=platops-help) is for raising support requests to the `Platform Operations` team.