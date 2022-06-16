# AksBuildAgent
### AKS Self-Hosted Windows Agent

- Set up Dockerfile
- Create an ACR.
```powershell
az group create --name aksbuildagent --location westus2
az acr create --resource-group aksbuildagent --name <Your_ACR_Name> --sku Basic
```
- Build the Dockerfile in the Cloud with a Task.
```powershell
az acr build --registry <Your_ACR_Name> -t buildagent:v1.0 --platform windows .
```
- Create a Personal Access Token into AzDo.
- Create an Agent Pool in AzDo.
- Enable Admin User in ACR.
- Create an Azure Container Instance.
```powershell
az container create --resource-group aksbuildagent --name <ACR_Name> --image <Image_URL> --restart-policy OnFailure --registry-login-server <ACR_Login_Server> --registry-username <ACR_UserName> --registry-password <Password> --os-type Windows --environment-variables 'AZP_URL=<AZDO_URL>' 'AZP_TOKEN=<PAT>' 'AZP_POOL=<Pool_Name>' 'AZP_AGENT_NAME=<Agent_Name>' 
```
- After some minutes the server will become available for the pipelines.
- Add agent pool with the pipeline.

### How to add agent with the pipeline ?
Check the 'pool' section.
```yaml
trigger:
- main

pool:
  name: akspool
  demand:
   - agent.name -equals DockerAgent1

variables:
- name: ResourceGroup
  value: arm_storageacct9
- name: ResourceGroup01
  value: arm_storageacct8
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


https://docs.microsoft.com/en-us/azure/devops/pipelines/agents/docker?view=azure-devops
