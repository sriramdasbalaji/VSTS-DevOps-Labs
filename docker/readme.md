## Docker Deployment for ASP.NETCORE Application with Visual Studio Team Services and Azure

## Overview

This lab shows how you can build custom images of <a href="http://dockr.ly/2zLiDPy">**Dockerized ASP.NETCORE**</a> application, and push those images to <a href="http://dockr.ly/2AJLgge"> **Private Repository** </a> (<a href="http://bit.ly/2jssVQy"> Azure Container Registry </a>), and then pull these images to deploy to containers in **Azure Web App** (Linux) using VSTS. Since we are using an **ASP.NETCORE** application it could be deployed to either **Windows** or **Linux** Containers.

Web App for Containers lets you bring your own Docker formatted container images and easily deploy and run them at scale with Azure. Combination of Team Services and Azure integration with Docker will enable you to:

1.  <a href="http://dockr.ly/2z2Qsi2"> Build </a> your own custom images using <a href="http://bit.ly/2jqGujv"> VSTS Hosted Linux agent </a>
2. <a href="http://dockr.ly/2hAZco0"> Push </a> and store images in your private repository
3. Deploy and  <a href="http://dockr.ly/2AJPaEW"> run </a> images inside containers

Below image helps us understand the VSTS DevOps flow with Docker: 

<img src="images/vstsdockerdevops.png">


## Pre-requisites

1.  You need a <b>Visual Studio Team Services Account</b> and <a href="http://bit.ly/2gBL4r4">Personal Access Token</a>

2. You need to install **Docker Integration** extension from <a href="http://bit.ly/2hurgK3">Visual Studio Marketplace</a>

## Setting up the Environment

Let us create an **Azure Container Registry** for storing the images we create after building them in VSTS. These images contain environment configuration details along with build settings.  An **Azure Web App with Linux OS** is also created where these custom built images could be deployed to run inside containers. 

1. Click on **Deploy to Azure** to spin up **Azure Container Registry** and **Azure Web App (Linux)**.

   <a href="https://portal.azure.com/#create/Microsoft.Template/uri/https%3A%2F%2Fraw.githubusercontent.com%2FMicrosoft%2FVSTS-DevOps-Labs%2Fdocker%2Fdocker%2Ftemplates%2Fazuredeploy.json" target="_blank">

    <img src="http://azuredeploy.net/deploybutton.png"/>
    </a> 

   <br/>

   <img src="images/createacr-linuxwebapp.png">

2. It takes upto **3 minutes** to provision the environment. 

   <img src="images/acrdeploymentsucceeded.png">

3. Below seen components are created post deployment. Click on newly created **Azure Container Registry**.

   <img src="images/postazuredeployment.png">

Description of components created:

   <table width="100%">
   <thead>
      <tr>
         <th width="50%"><b>Azure Component</b></th>
         <th><b>Description</b></th>
      </tr>
   </thead>
   <tr>
      <td><a href="http://bit.ly/2mwVYUz"><b>Container Registry</b></a></td>
      <td>Used to store images privately</td>
   </tr>
   <tr>
      <td><a href="http://bit.ly/2iYiCQx"><b>Storage Account</b></a> </td>
      <td>Storage account associated with container registry</td>
   </tr>
   <tr>
      <td><a href="http://bit.ly/2ALhdES"><b>App Service</b></a> </td>
      <td>Password </td>
   </tr>
   <tr>
      <td><a href="http://bit.ly/2AINQ5x"><b>App Service Plan</b></a> </td>
      <td>Enter email id associated with your account</td>
   </tr>
   </table>


4. Click on **Access keys** under **Settings** node. Note down the **Login server**,  **Username** and **Password**. We will need these in Exercise 1.

   <img src="images/acraccesskeys.png">

## Setting up the Project

1. Use <a href="https://vstsdemogenerator.azurewebsites.net" target="_blank">VSTS Demo Data Generator</a> to provision a project on your VSTS account 

2. Select **Docker** for the template.

3. Once the project is provisioned, select the URL to navigate to the project that you provisioned.

   <img src="">


## Exercise 1: Endpoint Creation

We have an **ASP.NETCORE code** provisioned by the demo generator system. We will leverage **Docker** components installed in **Hosted VSTS agent** to build and deploy custom images to containers.

1. In VSTS, navigate to **Services** by clicking on the gear icon, and click on **+ New Service Endpoint**. Click on **Azure Resource Manager** endpoint. Specify a **Connection name**, and choose your **Subscription** from the dropdown. This endpoint will be used to connect from VSTS project to Azure subscription.

   <img src="images/armendpoint.png">

   A pop-up window will be prompted to authorize this connectivity by entering email-id and password associated with the account. If you see a blank screen after clicking **OK**, disable any pop-up blocker in your browser and retry the step. 

2. Again, click on **+ New Service Endpoint**. Select **Docker Registry** from the list. This endpoint will be used to connect from VSTS project to Azure Container Registry, where images would be hosted.

   <img src="images/acrendpoint.png">

   Select **Others** for **Registry Type**. Map the details to **Docker Registry** from **ACR** (in azure portal) by passing the parameters as shown below:

  <table width="100%">
   <thead>
      <tr>
         <th width="50%"><b>Docker Registry</b></th>
         <th><b>Azure Container Registry</b></th>
      </tr>
   </thead>
   <tr>
      <td>Docker Registry (starts with https://)</a></td>
      <td>Login server</td>
   </tr>
   <tr>
      <td>Docker ID</a> </td>
      <td>Username</td>
   </tr>
   <tr>
      <td>Password</a> </td>
      <td>Password </td>
   </tr>
   <tr>
      <td>Email</a> </td>
      <td>Enter email id associated with your account</td>
   </tr>
   </table>


## Exercise 2: Update Build & Release definitions to point to new endpoints

1. Go to **Builds** under **Build and Release** tab, and edit the build definition **Docker**.

   <img src="images/build.png">

2. Click on **Process** section, and select newly added endpoint components from the dropdown under **Azure subscription** and **Azure Container Registry** as shown below.

   <img src="images/updateprocessbd.png">

   <br/>

   <table width="100%">
   <thead>
      <tr>
         <th width="50%"><b>Tasks</b></th>
         <th><b>Usage</b></th>
      </tr>
   </thead>
   <tr>
      <td><a href="http://bit.ly/2zlTspl"><b>Docker Compose</b></a> <img src="images/docker.png"></td>
      <td>Build, push, run Docker images and multi-container Docker applications</td>
   </tr>
   <tr>
      <td><a href="http://dockr.ly/2zHxLQ3"><b>Build, Push, Run </b></a> <img src="images/docs.png"></td>
      <td>Commands to work with dockerfile, respositories, tags, images</td>
   </tr>
   <tr>
      <td><a href="http://bit.ly/2iDhjpO"><b>Copy Files</b></a> <img src="images/copy-files.png"> </td>
      <td>Used to Copy files from source to destination folder using match patterns </td>
   </tr>
   <tr>
      <td><a href="http://bit.ly/2zGD6bn"><b>Publish Build Artifacts</b></a> <img src="images/publish-build-artifacts.png"> </td>
      <td> Used to share the build artifacts </td>
   </tr>
   </table>

3. Click on **Save**.

   <img src="images/savebd.png">

4. Click on **Releases** under **Build & Release** tab, and edit the Release Definition **Docker**.

   <img src="images/release.png">

5. Click on **Tasks**.

   <img src="images/release_tasks.png">

6. Click on **Deploy Azure App Service** task, and select newly added endpoint components from the dropdown under **Azure subscription** and **Azure Service name**. Manually update **Registry or Namespace** with **Azure Container Registry Login Server** as shown below and click on **Save**.

   <img src="images/updatedrd.png">


## Exercise 3: Update code to verify CI-CD pipeline

1. Let us do a code change to verify CI-CD pipeline. Go to **Code** tab, and navigate to the below path to edit the file- 

   >Docker/src/MyHealth.Web/Views/Home/**Index.cshtml**

   <img src="images/editcode.png">

2. Scroll down to line number **28**, and change the code from **JOIN US** to **JOIN US TODAY**, and click on **Commit**.

    <img src="images/lineedit.png">

3. Navigate to **Builds** section to see CI build in progress.

    <img src="images/in_progress_build.png">

4. You can see the build summary once completed.
    
    <img src="images/build_summary.png">
   

5. Once the Build is complete, navigate to **Releases** section to see CD in progress.

    <img src="images/release_in_progress.png">

6. Once the release is completed, you can see the summary which shows **Release Summary, Logs** etc.

    <img src="images/release_summary.png">

    <br/>

    <img src="images/release_logs.png">

7. After **Release** tasks are complete, copy the **Web App URL** from Azure Portal by navigating to the **Linux Web App** which was created at the beginning of this lab.

    <img src="images/getwebappurl.png">

8. Paste the above **URL** in browser to verify if the code we changed is reflected in the Web App.

    <img src="images/finalresult.png">

9. To see the images created you have to navigate to **Repositories** under **Azure Container Registry** in Azure Portal.

    <img src="images/imagesinrepo.png">


## Summary

With Visual Studio Team Services and Azure, we can have DevOps for dockerized applications where images would be deployed to Containers.

## Feedback

