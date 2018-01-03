# Deploying to Tomcat and MySQL

In this lab, you will learn how you can use Release Management(RM) in Visual Studio Team Services (VSTS) to deploy a Java web application to [Apache Tomcat](http://tomcat.apache.org/) with a MySQL database on Azure. Apache Tomcat is an open-source Java Servlet Container developed by the Apache Software Foundation (ASF). MySQL is a popular open-source relational database management system.

For this purpose of the lab, we will use Azure App Service and Azure Database for MySQL, a relational database service based on the open source MySQL Server engine. It is a fully managed database as a service offering capable of handing mission-critical workload with predictable performance and dynamic scalability. 

This lab will show how you can 
 * Create a new Azure App Service and configure it to use Apache Tomcat
 * Create a new MySQL database
 * Use Azure App Service Task to deploy a WAR file


## Pre-requisites
1. Microsoft Azure Account:</b> You will need a valid and active azure account for the labs.

1.  You need a <b>Visual Studio Team Services Account</b> and <a href="https://docs.microsoft.com/en-us/vsts/accounts/use-personal-access-tokens-to-authenticate">Personal Access Token</a>


## Exercise 1: Setting up Visual Studio Team Services

If you are following this lab from "Working with Jenkins, VSTS and Azure, you can skip this exercise. Otherwise,

1. Provision a new VSTS project using the <a href="https://vstsdemogenerator.azurewebsites.net" target="_blank">VSTS Demo Data Generator</a> 

1. Use  **MyShuttle** for the template


## Exercise 2: Creating Azure Web App and MySQL database

1. We will use the **Web App + MySQL** Azure template from the Azure Marketplace to create a Website and MySQL Database together to start developing even faster.
    <a href="https://portal.azure.com/#create/Microsoft.WebSiteMySQLDatabase" target="_blank"><img src="http://azuredeploy.net/deploybutton.png"/>
    </a>

1. This template provides three MySQL options : 
    * **Azure Database for MySQL(Preview)** provides a managed database service for app development. ***We will choose this option***
    * **ClearDB** is a Microsoft Partner solution for a fully integrated MySQL service on Azure
    * **MySQL in-app** is an App Service feature where MySQL database is created for your web app. In this scenario,MySQL server is running on the same instance side by side with your web server hosting the site. This is recommended to development purposes and has no additional cost.

    ![Create Web App+MySQL](images/createwebappmysql.png)


1. Wait for the Web App to be provisioned. Once it is ready, select the Web app. You can click the URL to see if it is correctly provisioned. 

1. As we are deploying a Java application, we need to change the web app's web container to Apache Tomcat. Click **Application Settings**. To change it to Tomcat, we will first need to install Java. Select a **Java Version** to install and then change **Web container** to use Apache Tomcat. For this purpose of the lab, we will choose ***Java 8*** and ***Apache Tomcat 9.0*** though the version number would not matter much for the simple app that we are deploying

    ![Setting Web container to Tomcat](images/webcontainer.png)

1. Click **Save** and wait for the update to be applied. Then navigate to the web app URL again and now you should see a different page

    ![Default Java App](images/defaultappjava.png)

# Exercise 3: Configuring MySQL database

1. Navigate to the resource group that you have created. You should see a **Azure Database for MySQL server** provisioned. Select the database server.  

![Resource Group](images/resourcegroup.png)

1. Select **Properties**. Note down **SERVER NAME** and **SERVER ADMIN LOGIN NAME** You may click the copy button next to each field to copy to the clipboard. 

    ![Database properties](images/dbproperties.png)

    In this example, the server name is *myshuttle-1-mysqldbserver.mysql.database.azure.com* and the admin user name is *mysqldbuser@myshuttle-1-mysqldbserver* 

1. We will use the MySQL command-line tool to establish a connection to the Azure Database for MySQL server. We will run the MySQL command-line tool from the Azure Cloud Shell in the browser.To launch the Azure Cloud Shell, click the `>_` icon in the top right toolbar.

1. Enter the following command

    ```HTML
    wget https://raw.githubusercontent.com/hsachinraj/azure-arm-templates/master/vstsazurejl_arm/mydbscript.script
    ```
    This should download the file that we want to execute on the server

1. Next, we will execute the SQL from the downloaded file on the database server. Enter the following command
    ````SQL
    mysql -h myshuttle-1-mysqldbserver.mysql.database.azure.com -u mysqldbuser@myshuttle-1-mysqldbserver -p < mydbscript.script
    ````
    Enter the password that you specified during provisioning the database

    ![Creating DB](images/createdatabase.png)

>This should create the database, tables and populate records for us.

## Updating the App Settings for the Web App

We have created the database that we need the app. Now, we need to update the connection strings for the web app to connect to the database correctly. There are multiple ways you can do this - but for the purpose of this lab, we will take a simple approach. We will update it directly on the Azure portal.

1. From the Azure portal, Select the Web app you provisioned. Select **Application Settings**. Scroll down to the **App Settings** section.    

1. Enter the following keys and values

|Key|Value|
|DB_USERNAME|Enter the MySQL server admin name|
|DB_Password|Enter the MySQL server admin password|
|DB_URL|the MySQL server address|
`
1. Click **Save**


## Deploying to App Service from VSTS 

1. Navigate to the VSTS project that you provisioned

1. Select **Build and Release** and then **Releases**

1. Select **MyShuttle** and click **Edit** to open the release definition

1. Select the **Dev** deployment process and make sure that **Azure Subscription** and **App Service Name** fields have the right values 

1. The subscription and the app service selected here will be used throughout the tasks in the process. We are using the **Deploy Azure App Service** task. This task is used to update Azure App Service to deploy Web Apps and WebJobs to Azure.  The task works on cross platform agents running Windows, Linux or Mac and uses the underlying deployment technologies of Web Deploy and Kudu. The task works for ASP.NET, ASP.NET Core 1 and Node.js based web applications.

>**Note:** The **Deploy Azure App Service** task works with  Azure Resource Manager APIs only.

