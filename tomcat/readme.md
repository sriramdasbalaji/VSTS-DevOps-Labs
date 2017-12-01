## Deploying to Tomcat and MySQL

In this lab, you will learn how you can use Release Management(RM) in Viusal Studio Team Services (VSTS) to deploy a Java web application to [Apache Tomcat](http://tomcat.apache.org/) with a MySQL database on Azure. Apache Tomcat is an open-source Java Servlet Container developed by the Apache Software Foundation (ASF). MySQL is a popular open-source relational database management system.

For this purpose of the lab, we will use Azure App Service and Azure Database for MySQL, a relational database service based on the open source MySQL Server engine. It is a fully managed database as a service offering capable of handing mission-critical workload with predictable performance and dynamic scalability. 

This lab will show how you can 
 * Create a new Azure App Service and configure it to use Apache Tomcat
 * Create a new MySQL database
 * Use Azure App Service Task to deploy a WAR file


## Pre-requisites
1. Microsoft Azure Account:</b> You will need a valid and active azure account for the labs.

1.  You need a <b>Visual Studio Team Services Account</b> and <a href="https://docs.microsoft.com/en-us/vsts/accounts/use-personal-access-tokens-to-authenticate">Personal Access Token</a>


## Exercise 1: Setting up Visual Studuio Team Services

If you are following this lab from "Working with Jenkins, VSTS and Azure, you can skip this exercise. Otherwise,

1. Provison a new VSTS project using the <a href="https://vstsdemogenerator.azurewebsites.net" target="_blank">VSTS Demo Data Generator</a> 

1. Use  **MyShuttle** for the template


## Exercise 2: Creating Azure Web App and MySQL database

1. We will use the **Web App + MySQL** Azure template from the Azure Marketplace to create a Website and MySQL Database together to start developing even faster. 
    ![Create Web App+MySQL](images/createappservicemysql.png)

1. This template provides three MySQL options : 
    * **Azure Database for MySQL(Preview)** provides a managed database service for app development 
    * **ClearDB** is a Microsoft Partner solution for a fully integrated MySQL service on Azure
    * **MySQL in-app** is an App Service feature where MySQL database is created for your web app. In this scenario,MySQL server is running on the same instance side by side with your web server hosting the site. This is recommended to development purposes and has no additional cost.***We will choose this option***

    <a href="https://portal.azure.com/#create/Microsoft.WebSiteMySQLDatabase" target="_blank"><img src="http://azuredeploy.net/deploybutton.png"/>
    </a>

1. Wait for the Web App to be provisoned. Once it is ready, select the Web app. You can click the URL to see if it is correctly provisioned. 

1. As we are deploying a Java application, we need to change the web app's web container to Apache Tomcat. Click **Application Settings**. To change it to Tomcat, we will first need to install Java. Select a **Java Version** to install and then change **Web container** to use Apache Tomcat. For this purpose of the lab, we will choose ***Java 8*** and ***Apache Tomcat 9.0*** though the version number would not matter much for the simple app that we are deploying

    ![Setting Web container to Tomcat](images/webcontainer.png)

1. Click **Save** and wait for the update to be applied. Then navigate to the web app URL again and now you should see a different page

    ![Default Java App](images/defaultappjava.png)


# Exercise 3: Configuring MySQL database
1. We chose to install the MySQL server on the same instance with the web server. Azure does not support accessing the MySQL in-app remotely using the MySQL CLI or other tools that access external endpoints. You can only access your database content using using the KUDU debug console or **[PHPMyAdmin](https://www.phpmyadmin.net/)**, a free and open source administration tool for MySQL. Note that this extension is automatically installed for you when you provision the web app. 

1. Select **MySQL In App** under **Settings** and then select **Manage**

    ![Manage MySQL In-App](images/mysqlinapp.png)

1. This should open the PHPMyAdmin home page in a separate tab. 

    ![PHP My Admin Page](images/phpmyadminhome.png)

1. Select the **SQL** tab and enter the following query:
    ```SQL
    CREATE USER 'user'@'localhost' IDENTIFIED BY 'password';
    ````
    You can replace the user name and password if you wish

1. We will now create the database and tables. Run the following SQL Query

    ```SQL
    use alm;

    CREATE TABLE `employees` (  `id` int(11) NOT NULL AUTO_INCREMENT,  `username` varchar(20) DEFAULT NULL, `password` varchar(20) DEFAULT NULL, PRIMARY KEY (`id`)
    ) ENGINE=InnoDB AUTO_INCREMENT=5 DEFAULT CHARSET=latin1;
    CREATE TABLE `fares` ( `id` int(11) NOT NULL AUTO_INCREMENT, `emp_id` int(11) DEFAULT NULL, `pickup` varchar(50) DEFAULT NULL COMMENT 'Street address',   `dropoff` varchar(50) DEFAULT NULL COMMENT 'Street address',`start` datetime DEFAULT NULL, `end` datetime DEFAULT NULL, `fare_charge` int(11) DEFAULT NULL COMMENT 'USD in pennies', `driver_fee` int(11) DEFAULT NULL COMMENT 'USD in pennies', `passenger_rating` tinyint(3) unsigned DEFAULT NULL COMMENT 'From 0 to 5', `driver_rating` tinyint(3) unsigned DEFAULT NULL COMMENT 'From 0 to 5',
    PRIMARY KEY (`id`), KEY `emp_id` (`emp_id`), CONSTRAINT `fares_ibfk_1` FOREIGN KEY (`emp_id`) REFERENCES `employees` (`id`) ) ENGINE=InnoDB AUTO_INCREMENT=11 DEFAULT CHARSET=latin1;


    INSERT INTO `employees` (`username`, `password`) VALUES ('fred', 'fredpassword');
    INSERT INTO `employees` (`username`, `password`) VALUES ('barney', 'barneypassword');
    INSERT INTO `employees` (`username`, `password`) VALUES ('wilma', 'wilmapassword');
    INSERT INTO `employees` (`username`, `password`) VALUES ('betty', 'bettypassword');
    INSERT INTO `fares` (`emp_id`, `pickup`, `dropoff`, `start`, `end`, `fare_charge`, `driver_fee`, `passenger_rating`, `driver_rating`) VALUES ((select `id` from `employees` where `username`='betty') , '87 Wooly Way, Topsoil, WA', '55 Mammoth Way, Bedrock, WA', '2014-06-19 16:13:00', '2014-06-19 16:34:00', '1527', '1145', '3', '4');
    INSERT INTO `fares` (`emp_id`, `pickup`, `dropoff`, `start`, `end`, `fare_charge`, `driver_fee`, `passenger_rating`, `driver_rating`) VALUES ((select `id` from `employees` where `username`='betty'), '18 Wooly Way, Bedrock, WA', '73 Mammoth Way, Limestone, WA', '2015-01-20 17:00:00', '2015-01-20 17:02:00', '743', '557', '2', '3');
    INSERT INTO `fares` (`emp_id`, `pickup`, `dropoff`, `start`, `end`, `fare_charge`, `driver_fee`, `passenger_rating`, `driver_rating`) VALUES ((select `id` from `employees` where `username`='betty'), '42 Mammoth Way, Rock Gardens, WA', '71 Hunting Lane, Bedrock, WA', '2015-04-28 00:20:00', '2015-04-28 00:40:00', '1682', '1261', '5', '2');
    INSERT INTO `fares` (`emp_id`, `pickup`, `dropoff`, `start`, `end`, `fare_charge`, `driver_fee`, `passenger_rating`, `driver_rating`) VALUES ((select `id` from `employees` where `username`='wilma'), '25 Hunting Lane, Topsoil, WA', '4 Mammoth Way, Limestone, WA', '2014-08-26 04:32:00', '2014-08-26 04:38:00', '900', '675', '1', '2');
    INSERT INTO `fares` (`emp_id`, `pickup`, `dropoff`, `start`, `end`, `fare_charge`, `driver_fee`, `passenger_rating`, `driver_rating`) VALUES ((select `id` from `employees` where `username`='wilma'), '1 Wooly Way, Rock Gardens, WA', '19 Wooly Way, Rock Gardens, WA', '2014-11-05 22:58:00', '2014-11-05 23:02:00', '718', '538', '2', '1');
    INSERT INTO `fares` (`emp_id`, `pickup`, `dropoff`, `start`, `end`, `fare_charge`, `driver_fee`, `passenger_rating`, `driver_rating`) VALUES ((select `id` from `employees` where `username`='wilma'), '36 Hard Rock Pl, Rock Gardens, WA', '38 Shale St, Limestone, WA', '2014-05-20 07:04:00', '2014-05-20 07:26:00', '567', '425', '4', '2');
    INSERT INTO `fares` (`emp_id`, `pickup`, `dropoff`, `start`, `end`, `fare_charge`, `driver_fee`, `passenger_rating`, `driver_rating`) VALUES ((select `id` from `employees` where `username`='fred'), '37 Hunting Lane, Rock Gardens, WA', '83 Brontosaurus Blvd, Topsoil, WA', '2014-04-08 16:00:00', '2014-04-08 16:24:00', '2878', '2158', '1', '5');
    INSERT INTO `fares` (`emp_id`, `pickup`, `dropoff`, `start`, `end`, `fare_charge`, `driver_fee`, `passenger_rating`, `driver_rating`) VALUES ((select `id` from `employees` where `username`='fred'), '84 Mammoth Way, Bedrock, WA', '40 Stegasaurus St, Limestone, WA', '2014-05-06 01:18:00', '2014-05-06 01:29:00', '1910', '1432', '5', '1');
    INSERT INTO `fares` (`emp_id`, `pickup`, `dropoff`, `start`, `end`, `fare_charge`, `driver_fee`, `passenger_rating`, `driver_rating`) VALUES ((select `id` from `employees` where `username`='barney'), '97 Stegasaurus St, Bedrock, WA', '93 Shale St, Bedrock, WA', '2014-12-13 10:43:00', '2014-12-13 11:20:00', '1869', '1401', '3', '2');
    INSERT INTO `fares` (`emp_id`, `pickup`, `dropoff`, `start`, `end`, `fare_charge`, `driver_fee`, `passenger_rating`, `driver_rating`) VALUES ((select `id` from `employees` where `username`='barney'), '43 Brontosaurus Blvd, Bedrock, WA', '51 Stegasaurus St, Rock Gardens, WA', '2014-05-17 10:41:00', '2014-05-17 10:53:00', '1460', '1095', '3', '2');
    ````

1. 




