# Azure DevOps and Azure Kubernetes Service Continuous Delivery

## Tools used
- Azure DevOps Pipelines
- Azure DevOps Repositories
- Azure Kubernetes Services
- Azure Container Registry

![azure architecture](https://github.com/DavidArayaSanabria/AKS---CI-CD/blob/36417ca6439bab78a2777d98f8396d439083d1dc/pictures/architecture.png)

The architecture shows logically the products used to deploy a web application to an Azure Kubernetes Service Cluster.

## The Cluster was created by using Azure's IaC too Bicep using the [bicep template](https://github.com/DavidArayaSanabria/AKS---CI-CD/blob/6902ba5d8f85248c6b0e62f7ac4ff1ace687e32e/Bicep/aks.bicep) and following commands:

```
az group create --name myResourceGroup --location eastus

az deployment group create --resource-group myResourceGroup --template-file main.bicep --parameters clusterName=toptalaksproject1
dnsPrefix=toptalaksproject linuxAdminUsername=daraya sshRSAPublicKey='~/.ssh/id_rsa.pub'
```

## Another important component is Azure Container Registry used to store the Docker Images generated by the Azure DevOps pipeline using this [bicep template](https://github.com/DavidArayaSanabria/AKS---CI-CD/blob/6902ba5d8f85248c6b0e62f7ac4ff1ace687e32e/Bicep/acr.bicep) and following commands:

```
az deployment group create --resource-group myResourceGroup --template-file main.bicep --parameters acrName=iactoptalprojectdavidaraya2023
```

## With the required AKS & ACR created the next steps take place in Azure DevOps.

## [The Web Application](https://github.com/DavidArayaSanabria/AKS---CI-CD/tree/main/app) is store in an Azure DevOps Repository 

Creating a pipeline to deploy to the Cluster first requires Azure DevOps to have permissions over the Azure Subscription, to achieving that a new service connection needs to be created, use the steps below:


![step 1 ](https://github.com/DavidArayaSanabria/AKS---CI-CD/blob/75cd5143575e17d778376b5282e82ce1adfc6cbe/pictures/7.png)

![step 2 ](https://github.com/DavidArayaSanabria/AKS---CI-CD/blob/75cd5143575e17d778376b5282e82ce1adfc6cbe/pictures/8.png)

![step 3 ](https://github.com/DavidArayaSanabria/AKS---CI-CD/blob/75cd5143575e17d778376b5282e82ce1adfc6cbe/pictures/9.png)

After creating the proper connection you can start creating the Pipeline following this steps:

![pipeline step 1 ](https://github.com/DavidArayaSanabria/AKS---CI-CD/blob/75cd5143575e17d778376b5282e82ce1adfc6cbe/pictures/1.png)

Select the repository where the application is hosted

![pipeline step 2 ](https://github.com/DavidArayaSanabria/AKS---CI-CD/blob/75cd5143575e17d778376b5282e82ce1adfc6cbe/pictures/2.png)

Select the option to Deploy to AKS

![pipeline step 3 ](https://github.com/DavidArayaSanabria/AKS---CI-CD/blob/75cd5143575e17d778376b5282e82ce1adfc6cbe/pictures/3.png)

Select your AKS and ACR created using Bicep 

![pipeline step 4 ](https://github.com/DavidArayaSanabria/AKS---CI-CD/blob/75cd5143575e17d778376b5282e82ce1adfc6cbe/pictures/4.png)

Make your commit and Save the Pipeline

![pipeline step 5 ](https://github.com/DavidArayaSanabria/AKS---CI-CD/blob/75cd5143575e17d778376b5282e82ce1adfc6cbe/pictures/4.1.png)

## Optionally you can create a [Self Hosted Azure DevOps Agent](https://learn.microsoft.com/en-us/azure/devops/pipelines/agents/agents?view=azure-devops&tabs=browser#install) for your Pipelines

For this you need to create a PAT or personal access token and get the agent version for Windows, Linux or MacOS

![self hosted agent 1 ](https://github.com/DavidArayaSanabria/AKS---CI-CD/blob/75cd5143575e17d778376b5282e82ce1adfc6cbe/pictures/10.png)

![self hosted agent 2 ](https://github.com/DavidArayaSanabria/AKS---CI-CD/blob/75cd5143575e17d778376b5282e82ce1adfc6cbe/pictures/11.png)

## Pipeline Build

When the Pipeline is run the output will look like this: 

![pipeline build ](https://github.com/DavidArayaSanabria/AKS---CI-CD/blob/48be19e8d2ad455714bc6c2d840c922bd0a4b4c6/pictures/12.jpeg)

On its Deploy Stage you need to allow it to do that to your ACR and AKS

![pipeline deploy 1 ](https://github.com/DavidArayaSanabria/AKS---CI-CD/blob/5c2741b0f58eed1baaef5abcf1615ffdfb4648b2/pictures/13.jpeg)

![pipeline deploy 2 ](https://github.com/DavidArayaSanabria/AKS---CI-CD/blob/5c2741b0f58eed1baaef5abcf1615ffdfb4648b2/pictures/14.jpeg)

One it has deployed you will be able to find the Public IP given to our application in a Kubernetes Service in this case  20.84.17.96

![pipeline deploy 3 ](https://github.com/DavidArayaSanabria/AKS---CI-CD/blob/5c2741b0f58eed1baaef5abcf1615ffdfb4648b2/pictures/15.jpeg)

## Connecting to the cluster using the 

```
az aks get-credentials --resource-group (rg-name) --name (cluster-name)
```
Allows you to run Kubectl commands such as:

![kubectl 1 ](https://github.com/DavidArayaSanabria/AKS---CI-CD/blob/5c2741b0f58eed1baaef5abcf1615ffdfb4648b2/pictures/16.jpeg)

For checking the Kubernetes Deployment used by our Application's products

## Some relevant AKS management commands:

Scaling the number of worker nodes in the cluster

```
- az aks scale --resource-group myResourceGroup --name (rg-name) --node-count (number of nodes) --nodepool-name <your node pool name>
```

Stopping the AKS cluster when is not on production to save money

```
- az aks stop --name myAKSCluster --resource-group (rg-name)

```

Increase the number of Pods running our Deployment

```

- kubectl scale --replicas=3 deployment/toptalaksproject

```

## Finally, you can visit 20.84.17.96 to view your running application or use the curl command

![curl ](https://github.com/DavidArayaSanabria/AKS---CI-CD/blob/5c2741b0f58eed1baaef5abcf1615ffdfb4648b2/pictures/17.jpeg)

# Conclusion 

The Azure DevOps pipeline is now connected to the AKS cluster and available to build and deploy any change on the code base.

For Backing up your AKS cluster the recommended tool is [Velero](https://learn.microsoft.com/en-us/azure/aks/operator-best-practices-storage#secure-and-back-up-your-data)


