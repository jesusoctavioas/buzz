# Introduction

Buzz is a scaling platform allowing Azure Virtual Machine Scale Sets (VMSS) to scale beyond the limits of a single set, allowing for hyper-scale stress tests, DDoS simulators and HPC use cases.

# Information

Buzz orchestrates a number of Azure components to manage high scale clusters of VMs running and performing the same actions, such as generating load on an endpoint, as in the implemented scenario.
Buzz uses Durable Functions runtime exposing the following APIs:

## Create Cluster: 

**POST:** [FUNCTION_URL]/PostCluster?code=[YOUR FUNCTION KEY]

body:
{
    "name": "LOAD",
    "scale": 1000
}

## Delete Cluster: 

**DELETE:** [FUNCTION_URL]/DeleteCluster?code=[YOUR FUNCTION KEY]

body:
{
    "name": "LOAD"
}

# Deploy to Azure

**1. create a storage**

az group create --name buzz-rg --location westeurope

az storage account create --name buzzdata --resource-group buzz-rg --location westeurope --sku Standard_LRS

az storage container create --name applications --account-name buzzdata public-access blob

az storage blob copy start --destination-blob wget.exe --destination-container applications --account-name buzzdata --source-uri https://eternallybored.org/misc/wget/releases/wget-1.20-win64.zip	

note the name and account key of the storage account

**2. register application for ARM RBAC**

az ad sp create-for-rbac --name "buzz-app-rbac" --role="Contribor" --scopes="/subscriptions/[YOUR SUBSCRIPTION ID]"

note the provided app id, password and tenant id

**3. create oms workspace**

https://docs.microsoft.com/en-us/azure/azure-monitor/learn/quick-create-workspace

note the workspace id and key

**4. create function**

az storage account create --name buzzfunctionstorage --location westeurope --resource-group buzz-rg --sku Standard_LRS

az functionapp create --name buzzfunctionapp --storage-account buzzfunctionstorage --consumption-plan-location westeurope --resource-group buzz-rg 

az functionapp config appsettings set --name buzzfunctionapp  --resource-group buzz-rg --settings FUNCTIONS_EXTENSION_VERSION=~2
 
**5. deploy and configure**

config!!!

az functionapp deployment source config --resource-group buzz-rg --name buzzfunctionapp --repo-url https://github.com/balteravishay/buzz.git

# More Resources

Full code story and design considerations here.