## Deploy ASP.NET application to Azure App Service using VSTS

This lab shows how to deploy an <a href="https://www.asp.net/">ASP.NET</a> application to Azure App Service with Visual Studio Team Services.

ASP.NET is an open source web framework for building modern web apps and services. ASP.NET creates websites based on HTML5, CSS, and JavaScript that are simple, fast, and can scale to millions of users.

## Pre-requisites

1. **Microsoft Azure Account:** You will need a valid and active azure account for the labs

2. You need a **Visual Studio Team Services Account** and <a href="http://bit.ly/2gBL4r4">Personal Access Token</a>


## Setting up the VSTS Project

1. Use <a href="http://bit.ly/2zPCeOM" target="_blank">VSTS Demo Data Generator</a> to provision a project on your VSTS account.

   <img src="images/vsts_demo_site.png">

2. Once the project is provisioned, select the URL to navigate to the project.

   <img src="images/navigate_to_vsts.png">


## Exercise 1: Endpoint Creation

Since the connections are not established during project provisioning, we will manually create the endpoints.

In VSTS, navigate to **Services** by clicking the gear icon, and click **+ New Service Endpoint**. Select **Azure Resource Manager**. Specify **Connection name**, select your **Subscription** from the dropdown and click **OK**. We use this endpoint to connect **VSTS** with **Azure**.

   <img src="images/endpoint_creation.png">

   You will be prompted to authorize this connection with Azure credentials.

   **Note**: Disable pop-up blocker in your browser if you see a blank screen after clicking OK, and retry the step.

## Exercise 2: Configure Release

Now that connections are established, we will manually map the endpoints to release definition.

1. Go to **Releases** under **Build & Release** tab, edit the release definition **PartsUnlimitedE2E** and select **Tasks**.

   <img src="images/release.png">

   <br/>

   <img src="images/release_2.png">

2. Under **Azure Deployment** task, update **Azure subscription** with the endpoint components from the dropdown and select the desired **location**.

   <img src="images/task1.png">

3. Under **Azure App Service Deploy** task, update **Azure subscription** with the endpoint components from the dropdown.

   <img src="images/task2.png">

## Exercise 2: Update Code

We will update the code to trigger CI-CD.

1. Go to the **Code** hub.

   <img src="images/code.png">

2. We have an **ASP.NET** app code provisioned by the demo generator system. We will deploy this to Azure app service.

3. We have a Continious Integration (CI) build setup to run upon a code commit. Let's make a simple change to the code to trigger the CI build.

4. Open the file **Index.cshtml** by navigating to the below path-
   
   > **PartsUnlimited-aspnet45/src/PartsUnlimitedWebsite/Views/Home/Index.cshtml**

   <img src="images/edit_code_1.png">

5. Edit the code. For this example, let's change **line 28** to increase discount from **50%** to **70%** 

   <img src="images/edit_code_2.png">

6. Select **Commit** to save and commit the changes. 

7. The code commit will trigger the CI build. Go to the **Build and Release** tab to see the CI build running in progress.

   <img src="images/build_overview.png">

   While the build is in progress, let's explore the build definition. The tasks that is used in the build definition are listed in the table below.

   <table width="100%">
   <thead>
      <tr>
         <th width="50%"><b>Tasks</b></th>
         <th><b>Usage</b></th>
      </tr>
   </thead>
   <tr>
      <td><a href="http://bit.ly/2ilmcHL"><b>Nuget Installer</b></a> <img src="images/nuget.png"></td>
      <td>Restores NuGet Packages/Dependencies for the solution </td>
   </tr>
   <tr>
      <td><a href="http://bit.ly/2xPrMUY"><b>Visual Studio Build</b></a> <img src="images/visual-studio-build.png"> </td>
      <td>Uses MSBuild arguments to build and package the solution as a zip file</td>
   </tr>
   <tr>
      <td><a href="http://bit.ly/2xPqJ7f"><b>Visual Studio Test</b></a> <img src="images/vstest.png"> </td>
      <td>Runs Unit Tests by using Visual Studio Test Runner </td>
   </tr>
   <tr>
      <td><a href="http://bit.ly/2grMxTQ"><b>Copy Files</b></a> <img src="images/copy-files.png"> </td>
      <td>Copy files from source to destination folder using match patterns </td>
   </tr>
   <tr>
      <td><a href="http://bit.ly/2yBgXde"><b>Publish Build Artifacts</b></a> <img src="images/publish-build-artifacts.png"> </td>
      <td>the build artifacts (package) will be published to VSTS which will be consumed in the release process </td>
   </tr>
   </table>
   <br/>

   <img src="images/build_in_progress.png">

8. Once the build is complete, you will see the summary which shows **Test Results, Code Coverage** etc as shown.

   <img src="images/build_summary.png">

## Exercise 3: Continuous Delivery

We have a release configured to deploy the application. It is associated to the build and triggered when the build is successful.

1. Go to **Releases** tab under **Build and Release** hub.

2. Select the **PartsUnlimitedE2E** definition and **Edit**.

3. We have three environments **Dev**, **QA** and **Production** in pipeline.

   <img src="images/release_pipeline_overview.png">

4. Go to the **Dev** environment, you will see 2 tasks are used as shown.

   <table width="100%">
   <thead>
      <tr>
         <th width="57%"><b>Tasks</b></th>
         <th><b>Usage</b></th>
      </tr>
   </thead>
   <tr>
      <td><a href="http://bit.ly/2ysg1It"><b>Azure Resource Group Deployment</b></a> <img src="images/arm.png"></td>
      <td>creates a resource group with the name <b>ASPDOTNET</b> which contains <b>App Service, App Service plan, SQL server, SQL database, Application Insights</b></td>
   </tr>
   <tr>
      <td><a href="http://bit.ly/2zkks4L"><b>Azure App Service Deploy</b></a> <img src="images/app-service-deploy.png"> </td>
      <td>deploys the package to azure app service</td>
   </tr>
   <tr>
   </table>

   We are using **Infrastructure as a Code** in the release pipeline with an ARM template to provision the required infrastructure on Azure.

5. You will see in progress release.

   <img src="images/in_progress_release.png">

6. Once the release is complete, you will see the summary.
   <img src="images/release_summary.png">

   <img src="images/release_logs.png">

7. Login to [Azure Portal](https://portal.azure.com) and search a **Resource Group** with the name **ASPDOTNET**.

   <img src="images/azure_resources.png">

8. Navigate to one of the WebApp from the resource group and you will see the application deployed successfully with the changes.

   <img src="images/partsunlimited_overview.png">

## Summary

With **Visual Studio Team Services** and **Azure**, we can continuously deploy **ASP.NET** applications.

## Feedback

## TBA
