# Why Deployment Stacks? A Consultant’s Perspective

## Introduction

Infrastructure as Code (IaC) is a fundamental practice for managing cloud infrastructure efficiently. Many organizations choose Bicep, a domain-specific language (DSL) for deploying Azure resources, due to its native Azure integration and ease of use compared to JSON-based ARM templates. However, Bicep lacks native state management, making it challenging to track and manage infrastructure changes over time.

When faced with this limitation, organizations often consider Terraform, which offers robust state management through external backends. But what if the internal IT team is already trained in Bicep, and switching to Terraform would introduce unnecessary complexity? This is where Azure Deployment Stacks come in.

Azure Deployment Stacks bring stateful management to Bicep deployments, allowing teams to track, update, and delete resources as a group—without leaving the Azure ecosystem. In this blog, we explore a real-world consulting scenario where Deployment Stacks became the ideal solution for an enterprise looking to enhance their IaC strategy while using azure native services.

---

## Real-World Scenario: The Challenge

### **Client Background**
- Large enterprise using Bicep for Azure infrastructure deployment.
- Hundreds of Bicep modules deployed across Development, QA, and Production environments.
- Internal IT staff are proficient in Bicep but have no Terraform experience.

### **Problem Statement**
- **Lack of state management** in Bicep makes tracking infrastructure changes difficult.
- **No dependency management** between resources, leading to inconsistent deployments.
- **Deleting resources is cumbersome** since Bicep does not track deployments as a unified group.

### **Considered Solutions**
- **Terraform:** Introduces state management but requires a new toolset, workflows, and external state storage.
- **Azure Deployment Stacks:** Provides state management, rollback capabilities, and dependency tracking while staying within the Bicep ecosystem.

### **Decision**
Azure Deployment Stacks as the best solution, enabling the organization to maintain their Bicep-based workflows while adding state tracking and lifecycle management.

---

## Consultant’s Approach: Explaining the Solution

### **Trade-Offs Explained to the Client**

| Approach               | Pros                                           | Cons                                              |
|-----------------------|---------------------------------|--------------------------------|
| **Bicep (Without State Management)** | Simple, Azure-native, existing team skills | No tracking of resource state, manual cleanup required |
| **Terraform** | Robust state management, external backend | Learning curve, external state storage needed |
| **Deployment Stacks (with Bicep)** | Azure-native, stateful tracking, lifecycle management | New feature, requires some process adjustments |

### **Why Deployment Stacks?**
- Tracks state of resources without requiring external storage.
- Supports rollbacks & deletions at the stack level.
- Integrates seamlessly with Azure DevOps for automated deployments.
- No need to train IT teams in a new toolset (e.g., Terraform).

---

## Hands-on Demo: Setting Up the First Deployment Stack

### **Prerequisites**
Ensure you have the following installed:
- Azure CLI
- Visual Studio Code with the Bicep extension
- Azure Subscription Access

### **Step 1: Create a Basic Deployment Stack with Bicep**

Create a 'stacks-rg.bicep' file with the following content:

```bicep
targetScope = 'subscription'

// ------------------
//    PARAMETERS
// ------------------

@description('Azure region where resources will be deployed')
param location string = 'canadaeast'

@description('Name of rsource group to be created')
param name string = 'stack-rg'

@description('Tags to be assigned to the resources')
param tags object = {
  Environment: 'Development'
  Owner: 'Admin'
}

// ------------------
// RESOURCE GROUP
// ------------------

resource resourcegroup 'Microsoft.Resources/resourceGroups@2024-11-01' = {
  location: location
  name: name
  tags: tags
}

```

### **Step 3: Deploy the Stack**

Execute the following command to deploy the stack:

```bash
az stack sub create --name MyFirstStack --location canadaeast --template-file stacks-rg.bicep --action-on-unmanage detachAll --deny-settings-mode none
```

Verify deployment:

```bash
az stack sub show --name MyFirstStack
```

### **Step 4: Managing Deployment Stacks**

List all deployment stacks in a subscription:

```bash
az stack sub list --output table
OR
az stack sub list
```

Delete the stack:

```bash
az stack sub delete --name MyFirstStack
```

---

## Key Takeaways

✅ Azure Deployment Stacks solve the state management challenge for Bicep.  
✅ They enable easier rollback, tracking, and dependency management.  
✅ Enterprises can continue using Bicep while gaining Terraform-like state control.  
✅ Ideal for Azure DevOps CI/CD integration, ensuring consistency across environments.  

---

## Next Steps

In the next part of this series, we’ll explore how to structure Deployment Stacks for enterprise-scale deployments, including best practices for multi-environment management. 

💡 Want to try it yourself? Fork the GitHub repository with the Bicep code and deployment scripts. 

🚀 Stay tuned for Part 2!

---

## Useful links
- https://learn.microsoft.com/en-us/cli/azure/stack?view=azure-cli-latest
- https://learn.microsoft.com/en-us/azure/azure-resource-manager/bicep/deployment-stacks?tabs=azure-powershell
