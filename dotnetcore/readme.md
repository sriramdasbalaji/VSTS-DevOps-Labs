## Deploy an ASP.NET Core application to Azure App Service using VSTS

This lab shows how to deploy an ASP.NET Core application to Azure App Service with Visual Studio Team Services.

<a href="https://docs.microsoft.com/en-us/aspnet/core/">ASP.NET Core</a> is a cross-platform, high-performance, open-source framework for building modern, cloud-based, Internet-connected applications. With ASP.NET Core, you can:

- Build web apps and services, IoT apps, and mobile backends.
- Use your favorite development tools on Windows, macOS, and Linux.
- Deploy to the cloud or on-premises
- Run on .NET Core or .NET Framework.

## Pre-requisites

1. **Microsoft Azure Account:** You need a valid and active azure account for the labs

2. You need a **Visual Studio Team Services Account** and <a href="http://bit.ly/2gBL4r4">Personal Access Token</a>

## Setting up the VSTS Project

1. Use <a href="https://vstsdemogenerator.azurewebsites.net" target="_blank">VSTS Demo Data Generator</a> to provision a project on your VSTS account.

   <img src="images/1.png">

2. Once the project is provisioned, select the URL to navigate to the project.

   <img src="images/">

## Exercise 1: Endpoint Creation

Since the connections are not established during project provisioning, we will manually create the endpoints.

In VSTS, navigate to **Services** by clicking the gear icon, and click **+ New Service Endpoint**. Select **Azure Resource Manager**. Specify **Connection name**, select your **Subscription** from the dropdown and click **OK**. We use this endpoint to connect **VSTS** with **Azure**.

   <img src="images/endpoint_creation.png">

   You will be prompted to authorize this connection with Azure credentials.

   **Note**: Disable pop-up blocker in your browser if you see a blank screen after clicking OK, and retry the step.


## Exercise 2: Configure Release

Now that connections are established, we will manually map the endpoints to release definition.

1. Go to **Releases** under **Build & Release** tab, edit the release definition **MyHealthClinicE2E** and select **Tasks**.

   <img src="images/create_release.png">

   <br/>

   <img src="images/release_2.png">

2. Under **Azure Deployment** task, update **Azure subscription** with the endpoint components from the dropdown and select the desired **location**.

   <img src="images/release_3.png">

3. Under **Azure App Service Deploy** task, update **Azure subscription** with the endpoint components from the dropdown.

   <img src="images/release_4.png">

## Exercise 3: Update Code

We will update the code to trigger CI-CD.

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

   <img src="images/6.png">

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
      <td>restores all the nuget dependencies like <b>ASP.NET Core Identity, ASP.NET Core session</b> etc. using dotnet command-line tool </td>
   </tr>
   <tr>
      <td><img src="images/npm.png"> <b>npm Install</b> </td>
      <td>installs npm packages (javascript dependencies) like <b>babelify, browserify</b> etc. for this project</td>
   </tr>
   <tr>
      <td><img src="images/bower.png"> <b>Bower Install</b></td>
      <td>is a package manager for web which manage components that contain HTML, CSS, JavaScript, fonts and even image files. Example: jquery, angular, webcomponentsjs etc.</td>
   </tr>
   <tr>
      <td><img src="images/gulp.png"> <b>Gulp</b></td>
      <td>gulp is used to compile sass files, uglify and compress js files </td>
   </tr>
   <tr>
      <td><img src="images/dotnetcore.png"> <b>Build</b></td>
      <td>builds the project and its dependencies into a set of binaries using dotnet command-line tool </td>
   </tr>
   <tr>
      <td><img src="images/dotnetcore.png"> <b>Test</b></td>
      <td>executes unit tests in this project using dotnet command-line tool. There are 6 unit tests. </td>
   </tr>
   <tr>
      <td><img src="images/dotnetcore.png"> <b>Publish</b></td>
      <td>compiles the application, reads through its dependencies specified in the project file, and publishes the resulting set of files to $(build.artifactstagingdirectory)</td>
   </tr>
   <tr>
      <td><img src="images/vstest.png"> <b>Publish Test Results</b></td>
      <td>used to publish test results including the formats for <b>JUnit, NUnit2, VSTest, NUnit3 and xUnit2</b> </td>
   </tr>
   <tr>
      <td><img src="images/copyfiles.png"> <b>Copy Files</b></td>
      <td>Used to Copy files from source to destination folder using match patterns </td>
   </tr>
   <tr>
      <td><img src="images/publishartifacts.png"> <b>Publish Build Artifacts</b></td>
      <td> Used to share the build artifacts </td>
   </tr>
   </table>
   <br/>

   <img src="images/7.png">

8. Once the build is completed, you can see the summary which shows **test results** etc as shown below.

   <img src="images/8.png">

## Continuous Delivery

We have a release pipeline configured to deploy the application. It is associated to the build and triggered when the build is successful. Let's look at the release pipeline.

1. Navigate to the **Releases** tab under **Build and Release** hub.

2. Select the **MyHealthClinicE2E** definition and choose **Edit**.

3. We have three environments **Dev**, **QA** and **Production**.

   <img src="images/9.png">

4. Go to the **Dev** environment, you can see we have 2 tasks being used. The tasks that is used in the release definition are listed in the table below..

   <table width="100%">
   <thead>
      <tr>
         <th width="57%"><b>Tasks</b></th>
         <th><b>Usage</b></th>
      </tr>
   </thead>
   <tr>
      <td><a href="http://bit.ly/2ysg1It"><b>Azure Resource Group Deployment</b></a> <img src="images/arm.png"></td>
      <td>Creates, Updates an existing resource group using ARM templates  </td>
   </tr>
   <tr>
      <td><a href="http://bit.ly/2zkks4L"><b>Azure App Service Deploy</b></a> <img src="images/app-service-deploy.png"> </td>
      <td>Updates Azure App Service to deploy WebApps </td>
   </tr>
   <tr>
   </table>

We are using **Infrastructure as a Code** in the release pipeline with an ARM template to provision the required infrastructure **(Web App and SQL database)** on Azure.

5. You can see in progress release as shown below.

   <img src="images/10.png">

6. Once the release is completed, you can see the summary which shows **Release Summary, logs etc**.

   <img src="images/11.png">

   <img src="images/12.png">

7. Login to [Azure Portal](https://portal.azure.com) and search a **Resource Group** with the name **dotnetcore** that would have got created. It would be associated with few other resources like **SQL server, SQL DB, WebApps** etc as shown below.

   <img src="images/13.png">

8. Navigate to one of the WebApp from the resource group and you should see the application is deployed successfully with the changes made earlier as shown.

   <img src="images/14.png">


