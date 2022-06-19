# Azure DevOps Self Hosted Agents in Azure Kubernetes Service

### AKS Self-Hosted Windows Agent

- Clone the Repo & login in Azure

```powershell
# Clone the repo
git clone https://github.com/21bshwjt/AksBuildAgent.git
# Change Directory path
cd C:\aksbuildagent\AksBuildAgent
# Login to Azure
az login
# Set to your Subscription
az account set --subscription "xxxxxxxx-xxxx-xxxx-xxxx-xxxxxxxxxxxx"
```

You can refer [MSFT KB](https://docs.microsoft.com/en-us/azure/devops/pipelines/agents/docker?view=azure-devops) for the same.

- Create an ACR.
```powershell
# Create a Resource Group
az group create --name aksbuildagent --location westus2
# Create Azure Container Registry
az acr create --resource-group aksbuildagent --name <Your_ACR_Name> --sku Basic
```
- Build the Dockerfile in the Cloud with a Task.
```powershell
az acr build --registry <Your_ACR_Name> -t buildagent:v1.0 --platform windows .
```
- [Create a Personal Access Token into AzDo.](https://docs.microsoft.com/en-us/azure/devops/pipelines/agents/v2-windows?view=azure-devops#authenticate-with-a-personal-access-token-pat:~:text=Authenticate%20with%20a%20personal%20access%20token%20(PAT))
- [Create an Agent Pool in AzDo.](https://docs.microsoft.com/en-us/azure/devops/pipelines/agents/pools-queues?view=azure-devops&tabs=yaml%2Cbrowser)
- Enable Admin User in ACR.

Go to your ACR --> Access Keys --> Admin user (Enable that option; pls refer the below screenshot)
<img src="https://github.com/21bshwjt/AksBuildAgent/blob/ecebd07cab0cf75c88083d82e5b75aba868be2be/images/ACR.png?raw=true" width="500" height="325">

- Create an Azure Container Instance.

```powershell
az container create --resource-group aksbuildagent --name <ACR_Name> --image <Image_URL> --restart-policy OnFailure --registry-login-server <ACR_Login_Server> --registry-username <ACR_UserName> --registry-password <Password> --os-type Windows --environment-variables 'AZP_URL=<AZDO_URL>' 'AZP_TOKEN=<PAT>' 'AZP_POOL=<Pool_Name>' 'AZP_AGENT_NAME=<Agent_Name>' 
```
- After few minutes agent will become available for the pipelines.
<img src="https://github.com/21bshwjt/AksBuildAgent/blob/d9eb0712f7dbee6567a0c8e72d87f9a327c51dae/images/akspool.png?raw=true" width="400" height="250">

- Add agent pool with the pipeline.

### How to add agent with the pipeline ?
Please check the 'pool:' section.
```yaml
trigger:
- main

pool:
  name: akspool
  demand:
   - agent.name -equals DockerAgent1

variables:
- name: ResourceGroup
  value: arm_storageacct01
- name: ResourceGroup01
  value: arm_storageacct02
- name: AzureDataCenter
  value: 'Central US'

resources:
  repositories:
    - repository: biswajit
      type: git
      name: 'Simple Server/Buildtemplates'

steps:
- template: storage_tag_containers01.yml@biswajit
- template: storage_tag_containers02.yml@biswajit
```

