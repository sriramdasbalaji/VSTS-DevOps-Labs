## Deploy ASP .Net application to Azure App Service using Team Services and Octopus

This lab shows how you can deploy an **ASP .Net application to Azure App Service using CI/CD pipeline in VSTS and Octopus**.

## Pre-requisites

1. **Microsoft Azure Account:** You will need a valid and active azure account for the labs

2. You need a **Visual Studio Team Services Account** and <a href="http://bit.ly/2gBL4r4">Personal Access Token</a>



## Setting up the project

1. Use <a href="https://vstsdemogenerator.azurewebsites.net" target="_blank">VSTS Demo Data Generator</a> to provision a project on your VSTS account.

   ![](images/1.png)

2. Select **PartsUnlimited** for the template.

   ![](images/2.png)

3. Once the project is provisioned, select the URL to navigate to the project that you provisioned.


## Provision Octopus Server on Azure
1. Click on **Deploy to Azure** to provision Octopus Server

   <a href="https://portal.azure.com/#create/Microsoft.Template/uri/https%3A%2F%2Fraw.githubusercontent.com%2Fsatishmkini%2Foctopus%2Fmaster%2Ftemplate.json"><img src="http://azuredeploy.net/deploybutton.png"></a> 

Provide **Resource Group Name** and **Octopus DNS Name**, make sure the names are unique. You should see the green checkmark as shown before you click on **Purchase**

<img src="images/A1.png">
<img src="images/A2.png">

Once the VM is provisoned, note down the DNS Name. We will need this to connect to **Octopus Server**  

<img src="images/A3.png">

## Connectand Configure Octopus Server

1. Login to octopus server by browsing DNS name

   >Default credentials **Username**: admin
**Password**: P2ssw0rd@123

   <img src="images/O1.png">

2. Click **Create environment** and **Add Environment**

   <img src="images/Create Environment.png">

   <br/>

   <img src="images/Add Environment.png">

3. Give environment name as **Dev** and **Save**

   <img src="images/DevEnvironment.png">

4. Add your Azure Subscription to Dev environment by clicking **ADD ACCOUNT**

   <img src="images/Add Account.png">

5. Enter **Name** and **Subecription ID** select **Use Management Certificate** and click **Save and Test**

   <img src="images/Create Account.png">

6. You will see a failure message click **OK**

   <img src="images/Verification Failed.png">

7. You will see a management certificate generated. Download this certificate

   <img src="images/Download Certificate.png">

8. Go to Azure portal, and click **Subscriptions**

   <img src="images/O8.png">

9. Go to your Subscription, and select **Management certificates**

   <img src="images/O9.png">

   <br/>

   <img src="images/O10.png">

10. Upload the subscription which you had downloaded in **step 7**

    <img src="images/O11.png">

    <img src="images/O12.png"  height="400px">

11. Go back to Octopus portal and click **Save and Test** you will see verification will be successful

    <img src="images/Verification Success.png">

## Link VSTS to Octopus Server

1. Create **New API Key** in octopus server. Under user profile, go to **MY API Key** and click **New API Key**

   <img src="images/API Key.png">

2. Give the purpose as VSTS Integration and click **Generate New**

   <img src="images/Generate New.png">

3. Note down the API Key.

   <img src="images/Key.png">

4. Create a **Service Endpoint** in VSTS. Go to Parts Unlimited team project in **VSTS** click on gear icon and click **Services**

   <img src="images/Endpoint.png">

   <br/>

   <img src="images/O22.png">

   <br/>

   <img src="images/endpointName.png">

## Build Project and Push the Package to Octopus Server

1. Go to **Builds** under **Build and Release** tab and click on **PartsUnlimitedE2E** build 

   <img src="images/Build Defination.png"> 
 
2. Click **Edit**

   <img src="images/EditBD.png">

3. In **Push Packages to Octopus** task, update **Octopus Deploy Server** then **Save and queue**. You will see the build progress.

   <img src="images/QBuild.png">

   <br/>

   <img src="images/Build Complete.png">

4. Once the build completes, go to Octopus Server. You will see the **Application Package** uploaded. Click **Upload package** to see the package.

   <img src="images/Pkg Uploaded.png">

   <br/>

   <img src="images/Pkg.png">

## Create Project in Octopus for Deployment

1. Go to Octopus dashboard and click **Create a project** and **ADD PROJECT**

   <img src="images/Project.png">

   <br/>

   <img src="images/Add Project.png">

2. Give the name for deployment project and click **SAVE**

   <img src="images/PUProject.png">

3. Once the project is created click **Define your deployment process**

   <img src="images/Define Process.png">

4. Add deployment step

   <img src="images/Add Step.png">

5. Search for **Azure Web App** template and **Add**

   <img src="images/O20.png">

6. Provide **Step Name** and Select **Package ID**

   <img src="images/PkgID.png">

   <br/>

   <img src="images/Azure.png">

7. Save | Click **Create Release**

   <img src="images/Release.png">

