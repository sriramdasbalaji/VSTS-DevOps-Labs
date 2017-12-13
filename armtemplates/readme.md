# Authoring release using Deployment Groups in VSTS

## Overview

A deployment group is a logical set of deployment target machines that have agents installed on each one. Deployment groups represent the physical environments; for example, "Dev", "Test", "UAT", and "Production". In effect, a deployment group is just another grouping of agents, much like an agent pool.

## Pre-requisites

1. **Microsoft Azure Account**: You need a valid and active azure account for the labs.

2.  You need a **Visual Studio Team Services Account** and <a href="https://docs.microsoft.com/en-us/vsts/accounts/use-personal-access-tokens-to-authenticate">Personal Access Token</a>

    <img src="images/vsts_demogen.jpg">

## Setting up the Environment


1. Click on **Deploy to Azure** to provision 7 VM's. It takes approximately 10-15 minutes to deploy.                                                                 
<a href="https://portal.azure.com/#create/Microsoft.Template/uri/https%3A%2F%2Fraw.githubusercontent.com%2FMicrosoft%2FVSTS-DevOps-Labs%2Fdeploymentgroups%2Farmtemplates%2Fazurewebsqldeploy.json" target="_blank">
<img src="http://azuredeploy.net/deploybutton.png"/>
</a>

2. Once the deployment is successful, you will see all the resources in the resource group in Azure Portal.
  
  <img src="images/vm_components.jpg">

3. Login to the VM using RDP with the VM credentials that you provided.

## Setting up the VSTS Project

1. Use <a href="">VSTS Demo Data Generator</a> to provision a project on your VSTS account.

image

2. Once the project is provisioned, select the URL to navigate to the project that you provisioned.

image

## Exercise 1: Creating and associating target machines to the Deployment group

[Deployment groups](https://docs.microsoft.com/en-us/vsts/build-release/concepts/definitions/release/deployment-groups/) in VSTS make it easier to organize the servers that you want to use to host your app. A deployment group is a collection of machines with a VSTS agent on each of them. Each machine interacts with VSTS to coordinate deployment of your app.

1. Go to **Deployment Groups** under **Build & Release** tab. Click **Add deployment group** to create one.

images

2.  You need to associate target machines to the Deployment Group. Every target machine in the deployment group requires the build and release agent to be installed. This can be achieved using the script that is generated as shown.

images 

3. . Run the registration script using **PowerShell** inside the VM that is available. When prompted by the script enter the **Personal Access Token**. 

images

 >The script that you've run will download and configure a windows agent on the VM so that it can receive new web deployment packages and apply them to IIS.

 4. Similarly, you can run the registartion script in all the target machines you want to associate with the deployment group as shown.

images

## Exercise 2: Trigger the Build

1. Once the environment is setup, queue the build. The build generates artifacts which we will use in our release to deploy.

## Exercise 3: Release to Deployment Groups

We have the artifacts which will be used to deploy to the machines using tag mechanism. Tags are used to limit deployment to specific sets of target servers.  

1. Go to **Releases** under **Build and Release** tab.you will have the release defnition created. Edit the release defenition and navigate over the **Deployment group Phase**

A phase is a logical grouping of tasks that defines the runtime target on which the tasks will execute. A deployment group phase executes tasks on the machines defined in a deployment group.

2. As shown in the below image you can choose the deployment group from the dropdown

images

3. As shown you can use machine tags to limit deployment to specific sets of target servers

images

4. You can define wether the release should happen parallely on the target machines or one target at a time

images


   <table width="100%">
   <thead>
      <tr>
         <th width="50%"><b>Tasks</b></th>
         <th><b>Usage</b></th>
      </tr>
   </thead>
      <tr>
      <td><a href="http://bit.ly/2zlTspl"><b>Manage IIS Website</b></a> <img src="images/iis-manage-icon.png"></td>
      <td>pulls image from default tag <b>latest</b> Use this task to create or update, start or stop, and recycle IIS Websites, IIS Web Applications, Virtual Directories, and IIS Application Pools. Supports adding application pools, and configuring bindings and authentication</td>
   </tr>
   <tr>
      <td><a href="https://docs.microsoft.com/en-in/vsts/build-release/tasks/deploy/iis-deploy"><b>Deploy IIS website</b></a> <img src="images/iis-deploy-icon.png"> </td>
      <td>Use this task to deploy IIS Websites and Virtual Applications using WebDeploy </td>
   </tr>
   </table>

   5. When release is executing, you see an entry in the live logs page for each server in the deployment group. After a release has completed, you can download the log files for every server to examine the deployments and resolve issues

images

6. Once the release is successful you can see that the web application has been hosted navigating to the below **URL** on your web browser.


       [Web Application URL](http://localhost:8000/)

7. The deployed web application will be as shown below.

images

# Summary

With Visual Studio Team Services and Azure, we can build and release dotnet applications to multiple target servers using Deployment Groups.

# Feedback
