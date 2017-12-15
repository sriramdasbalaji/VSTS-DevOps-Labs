# Authoring release using Deployment Groups in VSTS

## Overview

 [Deployment groups](https://docs.microsoft.com/en-us/vsts/build-release/concepts/definitions/release/deployment-groups/) is a logical set of deployment target machines that have agents installed on each one. Deployment groups represent the physical environments; for example, "Dev", "Test", "UAT", and "Production". In effect, a deployment group is just another grouping of agents, much like an agent pool.

## Pre-requisites

1. **Microsoft Azure Account**: You need a valid and active azure account for the labs.

2.  You need a **Visual Studio Team Services Account** and <a href="https://docs.microsoft.com/en-us/vsts/accounts/use-personal-access-tokens-to-authenticate">Personal Access Token</a>



## Setting up the Environment
Here we would provisoin seven VM's including six app tier with load balancing network, and a data tier.

1. Click on **Deploy to Azure** to provision. It takes approximately 10-15 minutes to deploy.                                                                 
<a href="https://portal.azure.com/#create/Microsoft.Template/uri/https%3A%2F%2Fraw.githubusercontent.com%2FMicrosoft%2FVSTS-DevOps-Labs%2Fdeploymentgroups%2Farmtemplates%2Fazurewebsqldeploy.json" target="_blank">
<img src="http://azuredeploy.net/deploybutton.png"/>
</a>

2. Once the deployment is successful, you will see all the resources in the resource group in Azure Portal.
   
   <img src="images/resources.png">




## Setting up the VSTS Project

1. Use <a href="">VSTS Demo Data Generator</a> to provision a project on your VSTS account.

   <img src="images/vstsdemogen.png">

2. Once the project is provisioned, select the URL to navigate to the project.

image

## Exercise 1: Endpoint Creation

Since the connections are not established during project provisioning, we will manually create the endpoints.

1. In VSTS, navigate to **Services** by clicking on the gear icon, and click on **+ New Service Endpoint**. Select **Azure Resource Manager**. Specify **Connection name**, select your **Subscription** from the dropdown and click **OK**. We use this endpoint to connect **VSTS** and **Azure**.

   <img src="images/service_endpoint.png"> 

## Exercise 2: Creating Deployment group

[Deployment groups](https://docs.microsoft.com/en-us/vsts/build-release/concepts/definitions/release/deployment-groups/) in VSTS make it easier to organize the servers that you want to use to host your app. A deployment group is a collection of machines with a VSTS agent on each of them. Each machine interacts with VSTS to coordinate deployment of your app.

1. Go to **Deployment Groups** under **Build & Release** tab. Click **Add deployment group** .

   <img src="images/add_deploymentgroup.png"> 

2. Provide name and click create. You will see the script generated.

   <img src="images/name_dg.png"> 

   <img src="images/script_dg.png"> 

## Exercise 3: Associating target VMs to Deployment Group

1. Login to app server **cust1websrv0** vm  through RDP with machine credentials provided during the provisoing of environment.

   <img src="images/rdp.png"> 

2. Open powershell console as an administrator  and execute the script generated in deployment group.


   <img src="images/powershell.png"> 

   <img src="images/powershell_agent.png">

3. Tag this VM as **web**
   
    <img src="images/powershell_web.png"> 

    <img src="images/powershell_complete.png"> 
  
3. Repeat **step 1** to **step 3** on other five web server.

4. Login to data server  **cust1Sqpip** vm.

5. perform **step 1** and **step 2** as above.

6. Tag this VM as **db**
  
   <img src="images/powershell_db.png"> 
       
7. Use the system user account to configure the agent.

   <img src="images/powershell_user.png"> 



## Exercise 4: Configure Release to Deployment Groups
 Now we have web server and data server ready for deployment. Deployment is done in phases.  

 > A phase is a logical grouping of tasks that defines the runtime target on which the tasks will execute. A deployment group phase executes tasks on the machines defined in a deployment group.

1. Go to Releases under **Build & Release** tab, edit the release definition **Deployment group** and select Tasks.

    <img src="images/release_tab.png"> 


     
    <img src="images/task.png"> 
 
   You will see task grouped under **Database deploy phase** and **IIS Deployment phase**

   <img src="images/phases.png"> 

**Database deploy phase**: Under this phase we use database deploy task which will deploy dacpac to database server 
    
<img src="images/dacpac.png">

This phase is linked to **db** tag.

<img src="images/db_tag.png">

This task will deploy Dacpac to server which is taged as **db** server.

**IIS Deployment phase**: Under this phase we deploy web app to web server.

This phase is linked to **web** tag.

<img src="images/iis.png">

**Maximum number of targets in parallel** allows us to set parallel deployment on multiple web server. In this case we have six server and if we set 50% parallel deployment will happen  on three servre at a time.

   <img src="images/targets.png">
 

2. Go to **Disconnect Azure Network Load Balancer** and update as shown.

   <img src="images/disconnect_lb.png">

This task will disconnect VMs from load balance network.

 **IIS Web App Manage** : This task will create web app on web apps server.

 <img src="images/web_manage.png">

**IIS Web App Deploy** : This task will deploy artifact to web apps server.

<img src="images/web_app.png">

3.  Go to **Connect vAzure Network Load Balancer** and update as shown.

    <img src="images/connect_lb.png">

This task will connect VMs from load balance network.

4. Click **save** and **create release**


   <img src="images/save.png">  

   <img src="images/create_release.png">


5. Once the release is successful go to any web server and browse below URL:


       Web Application URL(http://localhost:80/)

7. You will see web application as shown below.

   <img src="images/application.png">

  

# Summary

With Visual Studio Team Services and Azure, we can build and release dotnet applications to multiple target servers using Deployment Groups.

# Feedback
