## Deploy an ASP.NET Core application to Azure App Service using VSTS

This lab shows how to deploy an ASP.NET Core application to Azure App Service with Visual Studio Team Services.

<a href="https://docs.microsoft.com/en-us/aspnet/core/">ASP.NET Core</a> is a cross-platform, high-performance, open-source framework for building modern, cloud-based, Internet-connected applications. With ASP.NET Core, you can:

- Build web apps and services, IoT apps, and mobile backends.
- Use your favorite development tools on Windows, macOS, and Linux.
- Deploy to the cloud or on-premises
- Run on .NET Core or .NET Framework.

## Pre-requisites

1. **Microsoft Azure Account:** You need a valid and active azure account for the labs

2. You need a **Visual Studio Team Services Account** and <a href="https://docs.microsoft.com/en-us/vsts/accounts/use-personal-access-tokens-to-authenticate">Personal Access Token</a>

## Setting up the VSTS Project

1. Use <a href="https://vstsdemobuildertest.azurewebsites.net/?name=MyHealthClinic" target="_blank">VSTS Demo Data Generator</a> to provision a project on your VSTS account.

   <img src="images/vsts_demoGen.png">

2. Once the project is provisioned, select the URL to navigate to the project.

   <img src="images/project_provision.png">

## Exercise 1: Endpoint Creation

Since the connections are not established during project provisioning, we will manually create the endpoints.

In VSTS, navigate to **Services** by clicking the gear icon <img src="images/gear.png">, and click **+ New Service Endpoint**. Select **Azure Resource Manager**. Specify **Connection name**, select your **Subscription** from the dropdown and click **OK**. We use this endpoint to connect **VSTS** with **Azure**.

   <img src="images/endpoint_creation.png">

   You will be prompted to authorize this connection with Azure credentials.

   **Note**: Disable pop-up blocker in your browser if you see a blank screen after clicking OK, and retry the step.


## Exercise 2: Configure Release

Now that connections are established, we will manually map the endpoints to release definition.

>**Note** : You will encounter an error - **TFS.WebApi.Exception: Page not found** for Azure tasks in the release definition. This is due to a recent change in the VSTS Release Management API. While we are working on updating VSTS Demo Generator to resolve this issue, you can fix this by typing a random text in the **Azure Subscription** field and click the **Refresh** icon next to it. Once the field is refreshed, you can select the endpoint from the drop down.

1. Go to **Releases** under **Build & Release** tab, edit the release definition **MyHealthClinicE2E**.

   <img src="images/create_release.png">

2. Select **Tasks** and click **Dev**.

   <img src="images/release_2.png">

3. Under **Azure Deployment** task, update **Azure subscription** with the endpoint components from the dropdown and select the desired **location**.
   
   <img src="images/release_3.png">

4. Under **Azure App Service Deploy** task, update **Azure subscription** with the endpoint components from the dropdown. Under the **Slot** section enter the slot name as **Dev**.

   <img src="images/release_4.png">

5. Similarly update **Azure subscription** with the endpoint components for **QA** and **Production** environments. Go to **Tasks** and select **QA**.

   <img src="images/qa.png">

6. Under **Azure App Service Deploy** task, update **Azure subscription** with the endpoint components from the dropdown. Under the **Slot** section enter the slot name as **Staging**.

   <img src="images/qa_1.png">

7. Go to **Tasks** and select **Production**.

   <img src="images/prod.png">

8. Under **Azure App Service Deploy** task, update **Azure subscription** with the endpoint components from the dropdown and click **Save** to save the release definition.

   <img src="images/prod_1.png">

## Exercise 3: Triggering a Continuous Integration (CI) build

1. Go to the **Code** hub.

   <img src="images/code_hub.png">

2. We have an **DOTNETCORE** app code provisioned by the demo generator system. We will deploy this to Azure app service.

3. We have a Continious Integration (CI) build setup to run upon a code commit. Let's make a simple change to the code to trigger the CI build.

4. Open the file **Index.cshtml** by navigating to the below path-

   > **MyHealthClinic/src/MyHealth.Web/Views/Home/Index.cshtml**

   <img src="images/code_edit.png">

5. Edit the code. For this example, let's change **line 33** to change the text from **Login** to **Private Area**.

   <img src="images/update_code.png">

6. Select **Commit** to save and commit the changes.

7. The code commit will trigger the CI build. Go to the **Build** tab to see the CI build running in progress.

   <img src="images/build_in_progress.png">

   While the build is in progress, let's explore the build definition. The tasks that is used in the build definition are listed in the table below.

   <table width="100%">
   <thead>
      <tr>
         <th width="50%"><b>Tasks</b></th>
         <th><b>Usage</b></th>
      </tr>
   </thead>
   <tr>
      <td><img src="images/dotnetcore.png"> <b>Restore</b></td>
      <td>dotnet command-line tool restores all the package dependencies like <b>ASP.NET Core Identity, ASP.NET Core session</b> etc. required to build this project </td>
   </tr>
   <tr>
      <td><img src="images/npm.png"> <b>npm Install</b> </td>
      <td>npm task installs the npm packages (javascript dependencies) like <b>babelify, browserify</b> etc. required to build this project</td>
   </tr>
   <tr>
      <td><img src="images/bower.png"> <b>Bower Install</b></td>
      <td>We will use this task to manage components that contain HTML, CSS, JavaScript, fonts and even image files. Example: jquery, angular, webcomponentsjs etc.</td>
   </tr>
   <tr>
      <td><img src="images/gulp.png"> <b>Gulp</b></td>
      <td>gulp task compiles sass files, uglify and compress js files </td>
   </tr>
   <tr>
      <td><img src="images/dotnetcore.png"> <b>Build</b></td>
      <td>We will use dotnet command-line tool to build the project and its dependencies into a set of binaries</td>
   </tr>
   <tr>
      <td><img src="images/dotnetcore.png"> <b>Test</b></td>
      <td>dotnet command-line tool wil run unit tests as part of build process to ensure the code quality. This project contains 6 unit tests</td>
   </tr>
   <tr>
      <td><img src="images/dotnetcore.png"> <b>Publish</b></td>
      <td>We will use this task to create a package with published content for the web deployment</td>
   </tr>
   <tr>
      <td><img src="images/vstest.png"> <b>Publish Test Results</b></td>
      <td>We will use this task to publish the unit test results. </b> </td>
   </tr>
   <tr>
      <td><img src="images/copyfiles.png"> <b>Copy Files</b></td>
      <td>We will copy the zipped file and the ARM template to a staging directory.</td>
   </tr>
   <tr>
      <td><img src="images/publishartifacts.png"> <b>Publish Build Artifacts</b></td>
      <td>And finally, we will publish the files in the staging directory which were copied in the previous step.</td>
   </tr>
   </table>
   <br/>

8. Click on the build number to open the live console.

   <img src="images/build_number.png">

   <img src="images/build_in_progress_2.png">

9. Once the build is complete click on the build number, to see the summary which shows **Test Results** as shown.

   <img src="images/build_summary.png">

## Exercise 4: Continuous Delivery

We are using **Infrastructure as a Code** in the release pipeline. We have a release configured to deploy the application which is associated to the build and triggered when the build is successful.

1. Go to **Releases** tab under **Build and Release** hub.

2. Select the **MyHealthClinicE2E** definition, you will see the release in-progress.

   <img src="images/release.png">

3. While the release is in-progress, let's explore the tasks used. Click **edit** to see the release pipeline. We have three environments **Dev, QA** and **Production**.

   <img src="images/pipeline.png">

   >Go to the Dev environment, you will see 2 tasks are used. Let us explore the tasks.

   <img src="images/release_tasks.png">

   >**Azure Resource Group Deployment**: The project used in this lab contains frontend (Azure App Service) and backend (Azure SQL DB) services. We will provision these services as PAAS on Azure using ARM templates. This task will create the above services in a resource group **dotnetcore.**

   >**Azure App Service Deploy**: The task is used to deploy a Web project to the Azure App Service created above.

4. Click on **View releases**.

   <img src="images/view_releases.png">

5. Double click on the release to see the release summary.

   <img src="images/release_summary1.png">

   <br/>

   <img src="images/release_summary.png">

6. Login to [Azure Portal](https://portal.azure.com) and search a **Resource Group** with the name **dotnetcore**.

   <img src="images/azure_resources.png">

7. Navigate to one of the WebApp from the resource group and you will see the application is deployed successfully with the changes.

   <img src="images/mhc_web_app.png">

## Summary

**Visual Studio Team Services** simplifies creation of continuous integration and continuous delivery pipelines for your application to be deployed to Azure.

## Feedback

Please let <a href="mailto:devopsdemos@microsoft.com" target="_blank" >us</a> know if you have any feedback on this lab.
