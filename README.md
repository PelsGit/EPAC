# EPAC Implementation with Github Pipelines
Enterprise Policy As Code https://azure.github.io/enterprise-azure-policy-as-code/

# Enterprise Policy As Code (EPAC)

## Introduction

Azure policy is a very powerful way of deploying [guardrails] (https://learn.microsoft.com/en-us/azure/cloud-adoption-framework/ready/enterprise-scale/dine-guidance) for your organization. For years I have worked with customers understanding the need for having guardrails and what on the longer term the impact it can have on the Azure experience. 

Ensuring guardrails might be a fairly easy concept to understand, to master it requires a bit more finese and mastery of the technical skills. Azure policies need to be continouosly updated for changes in Compliancy organization standards, Resource Manager changes or changes to the built-in Policies. Failure to do so regularly can result in policies not working accordingly.

When I heard of the Enterprise Policy As Code initiative by Microsoft, I was immediatly excited to learn about the capabilities this will enable customers. 
Besides wanting to understand the value it can add for my customers, I wanted to know if I can use the assets as an out-of-the-box solution for my customers. 

This post is a guide where I attempt to deploy it using the assets from the repo, explain what it does in a certain degree, and give my honest opinion on the experience of using.

## Preparation

There are a couple things we need before starting:

- Azure tenant (working with policies does not incur costs, you are safe to create a tenant and test this feature)
- Github or Azure DevOps workspace (I will demo the Github variation of EPAC)
- Powershell latest version
- [EPAC-Powershell-Module] (https://azure.github.io/enterprise-azure-policy-as-code/start-implementing/#install-powershell-and-epac)

Besides the technical requirements, you need to have a good understanding of how EPAC works. Makes sense, failure to do so however can result in removal of policies or not having the correct policies scoped to the correct management groups.

To understand, read the [documentation] (https://azure.github.io/enterprise-azure-policy-as-code/start-implementing/).
I will highlight some important concepts here.

### Epac environments