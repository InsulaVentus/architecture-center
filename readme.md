# Multi-cloud blockchain DLT

Blockchain and Distributed Ledger Technology (DLT) networks are multi-party systems. Each party can have its own tools, methodology, and cloud provider.

Cloud providers' blockchain services can provide infrastructure management, but might require all parties to be in the same cloud or infrastructure, or might have limited region availability, scalability, or network segregation.

If several parties join forces to build a blockchain network, parties that use different cloud providers and infrastructures need a management platform with standard visibility, operations, and compliance across a wide range of resources and locations, regardless of hosting infrastructure.

This article explores how Azure Arc managed Kubernetes clusters can work with the open-source Blockchain Automation Framework (BAF) to build a cross-cloud blockchain solution that maximizes control and portability.

## Potential use cases

This approach supports:

- Heterogeneous deployments in a multi-cloud, multi-owner model, where each node is completely owned and managed by separate organizations.
- Central network status and compliance management and monitoring.

## Architecture

images/MultiCloud-DLT.svg

This solution provides a cloud-agnostic, multi-party DLT network. The scenario supports heterogenous deployments in a multi-cloud, multi-owner model where each owner can host their nodes anywhere and still be part of the network.

This example uses three parties. Party A uses [Azure Kubernetes Service (AKS)](/azure/aks/intro-kubernetes), Party B uses [GCP Google Kubernetes Engine (GKE)](https://cloud.google.com/kubernetes-engine), and Party C uses [Amazon Elastic Kubernetes Service (EKS)](https://aws.amazon.com/eks/). Each party hosts their nodes in a different location.

### Components

- [Kubernetes](https://kubernetes.io/) is the standard infrastructure to host both the ledger and the application. This example assumes three managed Kubernetes clusters, one each in AKS, Amazon EKS, and GCP GKE. You can host your cluster virtually anywhere.

- [Blockchain Automation Framework (BAF)](https://blockchain-automation-framework.readthedocs.io/) provides a consistent means for developers to deploy production-ready distributed networks across public and private clouds.
  - BAF is a great framework for managing the deployment, but it doesn't cater to central infrastructure management and monitoring.
  - BAF currently supports Quorum, Corda, and Hyperledger DLTs.

- [Azure Arc-enabled Kubernetes](/azure/azure-arc/kubernetes/overview) centrally manages Kubernetes clusters in any location. Azure Arc-enabled Kubernetes works with any Cloud Native Computing Foundation (CNCF)-certified Kubernetes cluster, including AKS engine on Azure, AKS engine on Azure Stack Hub, GKE, EKS, and VMware vSphere clusters.
  
- [Ambassador API Gateway](https://www.getambassador.io/products/api-gateway/) manages cross-node communications.
  
- BAF uses [HashiCorp Vault](https://www.hashicorp.com/products/vault) for certificate and key storage. To use BAF, you need at least one Vault server. BAF recommends one Vault per organization for production-ready projects.

Shared artifacts:
- [Azure DevOps](https://dev.azure.com/) provides application and infrastructure lifecycle management.
- [Ansible Controller on an Azure Linux virtual machine (VM)](https://azuredevopslabs.com/labs/vstsextend/ansible/) is the custom continuous integration and continuous delivery (CI/CD) agent for Azure DevOps.
- [Docker Registry](https://docs.docker.com/registry/) pulls ledger-specific images.
- [Azure Container Registry](https://azure.microsoft.com/services/container-registry/) stores and shares private, application-related container images.

## Considerations

For AKS best practices, see the [Baseline architecture for an Azure Kubernetes Service (AKS) cluster](/azure/architecture/reference-architectures/containers/aks/secure-baseline-aks). You can find similar best practices guidance for other cloud providers.

### Availability and scalability

Although Azure Arc can manage and monitor Kubernetes clusters, each cluster must independently implement scalability, high availability, and disaster recovery capabilities.

### Security

- [Ambassador API Gateway](https://www.getambassador.io/products/api-gateway/) manages cross-node communications.
- For internet connections, you can deploy a self-hosted, cloud-native API gateway through [Azure API Management](/azure/api-management/how-to-deploy-self-hosted-gateway-azure-kubernetes-service).
- Alternatively, you can use an [External DNS](https://github.com/kubernetes-sigs/external-dns) like [Azure DNS](https://azure.microsoft.com/services/dns).
- You can achieve private connections over IPSec with tools like [Submariner](https://github.com/submariner-io/submariner).

## Deploy this scenario

1. For this example, create managed Kubernetes clusters in AKS, GKE, and EKS, and onboard the clusters to Azure Arc:
   - [Onboard an existing cluster to Azure Arc](https://azurearcjumpstart.io/azure_arc_jumpstart/azure_arc_k8s/general/onboard_k8s/)
   - [Create and onboard Amazon Elastic Kubernetes Service](https://azurearcjumpstart.io/azure_arc_jumpstart/azure_arc_k8s/eks/eks_terraform/)
   - [Create and onboard Google Kubernetes Engine](https://azurearcjumpstart.io/azure_arc_jumpstart/azure_arc_k8s/gke/gke_terraform/)
1.  Follow steps for installing and configuring [BAF prerequisites](https://blockchain-automation-framework.readthedocs.io/en/latest/prerequisites.html).
1.  (Optional) [Create an Azure DevOps organization and project](/azure/devops/organizations/accounts/create-organization), then clone the BAF repo into the new Azure DevOps project.
1.  (Optional) Create an [Ansible Controller VM](https://azuredevopslabs.com/labs/vstsextend/ansible/) in Azure as the custom build agent to deploy BAF components.

## Pricing

To estimate Azure resources costs, use the [Azure pricing calculator](https://azure.microsoft.com/pricing/calculator/).
