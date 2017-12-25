## Automate Deployment Using Octopus Deploy & VSTS

[Octopus Deploy](https://Octopus.com) is an automated deployment server that makes it easy to automate the deployment of ASP.NET web applications, Java applications, database updates, NodeJS application, and custom scripts into development, test, and production environments.

This lab shows how you can deploy an ASP.NET application to Azure App Service using CI-CD pipeline with VSTS and Octopus.

## Pre-requisites

1. **Microsoft Azure Account:** You will need a valid and active azure account for the labs

2. You need a **Visual Studio Team Services Account** and <a href="http://bit.ly/2gBL4r4">Personal Access Token</a>



## Setting up the project

1. Use <a href="https://vstsdemogenerator.azurewebsites.net" target="_blank">VSTS Demo Data Generator</a> to provision a project on your VSTS account.

   ![](images/1.png)

2. Once the project is provisioned, select the URL to navigate to the project that you provisioned.


## Setting up the Environment

We will use ARM template to provision below resources on Azure:

- VM with Octopus server

- #To be added

1. Click on **Deploy to Azure** to provision Octopus Server.

   <a href="https://portal.azure.com/#create/Microsoft.Template/uri/https%3A%2F%2Fraw.githubusercontent.com%2FMicrosoft%2FVSTS-DevOps-Labs%2Foctopus%2Foctopus%2Farm%2520template%2Ftemplate.json"><img src="http://azuredeploy.net/deploybutton.png"></a> 

   Provide the following details as shown:

   - **Subscription**: Choose your Azure Subscription

   - **Resource Group**: Provide a name for resource group as  **Octopus**

   - **Location**: Select the location to **South Central US**

   - **Octopus Dns Name**: Provide a unique name.

2.  It takes approximately x minutes to deploy.

    <img src="images/DeployOctopus.png">


3. Once the VM is provisoned, note down the **DNS** Name. We will need this to connect to Octopus Server.

   >**Note**: In Azure portal, go to the VM overview which was provisioned and copy the DNS name as shown. 

   <img src="images/A3.png">

## Exercise 1: Configure Octopus Server

In this exercise, we will create **Deployment Environment** in Octopus server. Since we are deploying the application to Azure App Service, we will link the environment to Azure using **Management Certificate**.

1. Login to Octopus server using DNS name from your browser. Use the below credentials to login.

   - **Username**: admin
   - **Password**: P2ssw0rd@123

   <br/>

   <img src="images/O1.png">

2. Click **Create environment** and **Add Environment**.

   <img src="images/Create Environment.png">

   <br/>

   <img src="images/Add Environment.png">

3. Provide the environment name as **Dev** and click **Save**.

   <img src="images/DevEnvironment.png">

4. You will see **Dev** environment. Click on **Dashboard**

   <img src="images/Dev.png">

4. Add **Azure Subscription** to **Dev** environment by clicking on **ADD an account** and **ADD ACCOUNT**

   <img src="images/AddanAccount.png">
   <br/>
   
   <img src="images/Add Account.png">

5. Enter the following details as shown:

   - **Name**: Provide a name as **Azure Deployment**
   - **Subecription ID**: Your [Azure Subscription ID](https://blogs.msdn.microsoft.com/mschray/2016/03/18/getting-your-azure-subscription-guid-new-portal/)
   - **Authentication Method**: Set to **Use Management Certificate** 

   <br/>

   <img src="images/Create Account.png">

6. You will see a failure message because the management certificate is not uploaded to Azure portal. Click **OK**

   <img src="images/Verification Failed.png">

7. You will see a management certificate will be generated. Download this certificate.

   <img src="images/Download Certificate.png">

8. To upload the certificate to azure, go to [Azure Portal](portal.azure.com), and click on **Subscriptions**.

   <img src="images/O8.png">

9. Click on your Subscription.

   <img src="images/O9.png">

10. Scroll down and click **Management certificates**.

    <img src="images/O10.png">

11. Click **Upload** to upload the certificate which you downloaded in the step 7.

    <img src="images/O11.png">

    <br/>

    <img src="images/O12.png"  height="400px">

12. Go back to Octopus portal and click **Save and Test**. You will see the verification for azure connection will be successful.

    <img src="images/Verification Success.png">

## Exercise 2: Link VSTS to Octopus Server

In this exercise we will create an **API** key in Octopus. This key is required to link VSTS with Octopus.

1. Under user profile, go to **MY API Key** and click **New API Key** to create one.

   <img src="images/API Key.png">

2. Give the purpose as **VSTS Integration** and click **Generate New**.

   <img src="images/Generate new.png">

3. Note down the API Key.

   <img src="images/Key.png">

4. Create a **Service Endpoint** in VSTS. Go to Parts Unlimited team project in **VSTS**, click on gear icon and click **Services**.

   <img src="images/Endpoint.png">

5. Click **+ New Service Endpoint** and select **Octopus Deploy** from the dropdown.

    <img src="images/O22.png">

6. Provide **Connection name**, **URL** of Octopus server and **API Key** and click **OK**. 

   <img src="images/endpointName.png">

7. You will see service endpoint created successfully.

   <img src="images/endpointSuccess.png">

## Exercise 3: Push the Package to Octopus Server

In this exercise, we will build ASP.NET application and push the generated web package to Octopus Server.

1. Go to **Builds** under **Build and Release** tab and click on **PartsUnlimitedE2E** definition.

   <img src="images/Build Defination.png"> 
 
2. Click **Edit**.

   <img src="images/EditBD.png">

3. In **Push Packages to Octopus** task, update **Octopus Deploy Server** and click **Save and queue**.

   <img src="images/QBuild.png">

4. Once the build is completed, you will see the build summary.

   <img src="images/Build Complete.png">

5. Go to Octopus Server. You will see the green check mark showing **Application PACKAGING** uploaded successfully. Click **Upload package** to see the package.

   <img src="images/Pkg Uploaded.png">

   <br/>

   <img src="images/Pkg.png">

## Exercise 4: Project Creation in Octopus

[Projects](https://octopus.com/docs/deploying-applications/deployment-process/projects) allow you to define all the details required to deploy a project including the steps to run and variables to configure it.
In this exercise, we will create project in Octopus which will deploy the package to **Azure Web Service**

1. Go to Octopus dashboard and click **Create a project** and **ADD PROJECT**.

   <img src="images/Project.png">

   <br/>

   <img src="images/Add Project.png">

2. Give the project name **Parts Unlimited** and click on **SAVE**.

   <img src="images/PUProject.png">

3. Once the project is created, click **Define your deployment process**. The [deployment process](https://octopus.com/docs/deploying-applications/deployment-process) is like a recipe for deploying your software.

   <img src="images/Define Process.png">

4. Click on **ADD STEP**.

   <img src="images/Add Step.png">

5. Search for **Azure Web App** template and **Add**.

   <img src="images/O20.png">

6. Provide **Name** and select **Package ID**.

   <img src="images/PkgID.png">

   <br/>

   <img src="images/Azure.png">

7. Save and click on **Create Release**.

   <img src="images/Release.png">

