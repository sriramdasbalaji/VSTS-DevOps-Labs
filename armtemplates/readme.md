# Authoring release using Deployment Groups in VSTS

## Overview

A deployment group is a logical set of deployment target machines that have agents installed on each one. Deployment groups represent the physical environments; for example, "Dev", "Test", "UAT", and "Production". In effect, a deployment group is just another grouping of agents, much like an agent pool.

## Pre-requisites

1. **Microsoft Azure Account**: You need a valid and active azure account for the labs.

2.  You need a **Visual Studio Team Services Account** and <a href="https://docs.microsoft.com/en-us/vsts/accounts/use-personal-access-tokens-to-authenticate">Personal Access Token</a>

    <img src="images/vsts_demogen.png">

## Deployment group

Click on **Deploy to Azure** to provision VM.                                                                   
<a href="https://portal.azure.com/#create/Microsoft.Template/uri/https%3A%2F%2Fraw.githubusercontent.com%2FMicrosoft%2FVSTS-DevOps-Labs%2Fdeploymentgroups%2Farmtemplates%2Fazurewebsqldeploy.json" target="_blank">
<img src="http://azuredeploy.net/deploybutton.png"/>
</a>
