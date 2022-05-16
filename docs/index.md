# Introduction

![high-level-overview](./assets/overview.png)

Identity Manager Operator is a Kubernetes Operator that automates the creation and management of pod identities.

## What problem does Identity Manager Operator solve?

When an application running in a Pod needs to connect to a Cloud Service (such as Amazon SQS, Azure Event Hub, Google BigQuery Storage API), it needs API credentials. These API credentials need to be short-lived and fine grained so that a Pod gets only the required permissions. In each cloud provider, this is solved differently:

- Amazon EKS: <a href="https://docs.aws.amazon.com/eks/latest/userguide/iam-roles-for-service-accounts.html" target="_blank">IAM Roles for Service Accounts</a>
- Google GKE: <a href="https://cloud.google.com/kubernetes-engine/docs/concepts/workload-identity" target="_blank">Workload Identity</a>
- Azure AKS: <a href="https://azure.github.io/azure-workload-identity/docs/introduction.html" target="_blank">Azure Workload Identity</a>

Kubernetes administrators have to deal with the following:

- Creating fine grained Roles & Policies as per application needs
- Creating and managing Kubernetes Service Accounts that provides identities to Pods by mapping to specific Roles & Policies

Performing the above activities manually doesn't scale for large environments that comprises of many applications. Applications also evolve rapidly requiring dynamic modification of Service Accounts to provide access to more cloud services.

Identity Manager Operator automates the entire lifecycle of creating and managing pod identities. It takes care of the following:

- Dynamically creating fine grained IAM Roles & Policies as requested
- Provisioning Kubernetes Service Accounts
- Attaching the Kubernetes Service Accounts to Pods

## Kicked Off By

[![invisibl-cloud-logo](./assets/invisibl-cloud-logo.png)](https://invisibl.io)