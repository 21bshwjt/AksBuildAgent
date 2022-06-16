# AksBuildAgent
AKS Self-Hosted Windows Agent

- Set up Dockerfile
- Create an ACR.
```powershell
az group create --name aksbuildagent --location westus2
az acr create --resource-group aksbuildagent --name <Your_ACR_Name> --sku Basic
```
- Build the Dockerfile in the Cloud with a Task.
- Create a Personal Access Token into AzDo.
- Create an Agent Pool in AzDo.
- Enable Admin User in ACR.
- Create an Azure Container Instance.
- After some minutes the server will become available for our pipelines.

### We need to add agent pool with our pipeline.

https://docs.microsoft.com/en-us/azure/devops/pipelines/agents/docker?view=azure-devops
