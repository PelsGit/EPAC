# EPAC Implementation with Github Pipelines
Enterprise Policy As Code https://azure.github.io/enterprise-azure-policy-as-code/

# Enterprise Policy As Code (EPAC)

## Introduction

Azure policy is a very powerful way of deploying [guardrails](https://learn.microsoft.com/en-us/azure/cloud-adoption-framework/ready/enterprise-scale/dine-guidance) for your organization. For years I have worked with customers understanding the need for having guardrails and what on the longer term the impact it can have on the Azure experience. 

Ensuring guardrails might be a fairly easy concept to understand, to master it requires a bit more finese and mastery of the technical skills. Azure policies need to be continouosly updated for changes in Compliancy organization standards, Resource Manager changes or changes to the built-in Policies. Failure to do so regularly can result in policies not working accordingly.

When I heard of the Enterprise Policy As Code initiative by Microsoft, I was immediatly excited to learn about the capabilities this will enable customers. 
Besides wanting to understand the value it can add for my customers, I wanted to know if I can use the assets as an out-of-the-box solution for my customers. 

This post is a guide where I attempt to deploy it using the assets from the repo, explain what it does in a certain degree, and give my honest opinion on the experience of using.

## Preparation

There are a couple things we need before starting:

- Azure tenant (working with policies does not incur costs, you are safe to create a tenant and test this feature)
- Github or Azure DevOps workspace (I will demo the Github variation of EPAC)
- Powershell latest version
- [EPAC-Powershell-Module](https://azure.github.io/enterprise-azure-policy-as-code/start-implementing/#install-powershell-and-epac)

Besides the technical requirements, you need to have a good understanding of how EPAC works. Makes sense, failure to do so however can result in removal of policies or not having the correct policies scoped to the correct management groups.

To understand, read the [documentation](https://azure.github.io/enterprise-azure-policy-as-code/start-implementing/).
I will highlight some important concepts here.

### Epac environments and strategy

EPAC has the posibility to run in different so called environments. These can be a test environment or a different tenant.
You can also let the EPAC modules run the Policy state for all environments. It is very important to understand this concept of strategy. If you select the strategy 'full' for example, the EPAC modules will delete any existing policies from the scope you selected and replace with the policies in the repo.

The strategy and environments are defined in the global-settings.jsonc file. I have used the following:

``` json
{
    "$schema": "https://raw.githubusercontent.com/Azure/enterprise-azure-policy-as-code/main/Schemas/global-settings-schema.json",
    "pacOwnerId": "933a18d6-5381-45fd-8260-c0a9e8e3bbbb",
    "pacEnvironments": [
        {
            "pacSelector": "epac-dev",
            "cloud": "AzureCloud",
            "tenantId": "<TENANT_ID>",
            "deploymentRootScope": "/providers/Microsoft.Management/managementGroups/EpacDev",
            "desiredState": {
                "strategy": "ownedOnly",
                "keepDfcSecurityAssignments": false
            },
            "managedIdentityLocation": "westeurope"
        },
        {
            "pacSelector": "tenant",
            "cloud": "AzureCloud",
            "tenantId": "<TENANT_ID>",
            "deploymentRootScope": "/providers/Microsoft.Management/managementGroups/pels",
            "desiredState": {
                "strategy": "ownedOnly",
                "keepDfcSecurityAssignments": false
            },
            "managedIdentityLocation": "westeurope"
        }
    ]
}
```
As you can see, I have two environments and use the `ownedOnly` strategy. This is because I am running this in a managed tenant which requires certain policies from corp level to run. I don't want to delete these existing policies. If you want to allow the EPAC to manage all the policies, which is recommended according to the EPAC docs, you use the `full` strategy.

The `pacSelector` is where to specify the environments. I am going to use a development scope prior to deploying to production. If you use a multi-tenant approach, you can specify this here as well.

I will not go in-depth on this code, I would advise to read the following documentation to give a clear understanding: https://azure.github.io/enterprise-azure-policy-as-code/settings-global-setting-file/

### EPAC modules

We will need to install the required modules to run the EPAC scripts. Follow the guidance here: https://azure.github.io/enterprise-azure-policy-as-code/start-implementing/#install-powershell-and-epac.

Once installed, the essential folders and files need to be created. The following command will fetch these from the Github Repo and put them locally in your folder
`New-HydrationDefinitionFolder -DefinitionsRootFolder Definitions`

![EPAC definition folder](./docs/Screenshot%202024-05-03%20at%2015.44.59.png)

The script runs and creates all the required folders and Policies which it currently has in the repository. It also creates a boilerplate global-settings.jsonc file. Use this to populate the required settings. 

### Define CI/CD Strategy

#### Disclaimer
When you have the essential folders and powershell modules, this step is completely optional. 
Basically, you have two ways to implement EPAC

- Use the scripts as modules
- Use the CI/CD pre-built pipelines on the EPAC repo.

So yeah, you can just use the scripts, create your own CI/CD pipelines and you are fully in control of the flow.

I am going to continue using the pre-built CI/CD modules.

#### Choose your repo

The built-in EPAC modules have two choices when it comes to what repository you want to use:

- Azure DevOps
- Github

More information on the options you can find here: https://azure.github.io/enterprise-azure-policy-as-code/ci-cd-overview/

I checked both options. Azure DevOps currently has one implementation strategy, which basically deploys directly to production. This will of course change in the future I would imagine.

The Github gives you more built-in options:

- Simplified Github Flow

![Simplifiedflow](https://azure.github.io/enterprise-azure-policy-as-code/Images/epac-github-flow.png)

In a nutshell: With this flow you will perform a deployment to a Dev management group and finish with a deployment to individual production management groups. The flow from Dev to Production is done after you Pull Request a feature branch to the main branch.

1. Create a feature branch named `feature/**`
2. Make changes in the feature branch
3. Commiting changes in the definition folders or flow folders will trigger the CI/CD
4. Changes are planned in a seperate pipeline, the output is are files containing the changes to policies and corresponding role assignments
5. If changes are found, the changes are pushed to the Dev Management Group
6. A plan to production is done using the credentials of the Production environment
7. When all changes are finalized and tested, the feature branch needs to be merged to the main branch.
8. When the code is merged with the main branch and changes are located, the same process from 4 and 5 will repeat on the production management groups.

- Advanced Release flow

![advancedrelease](https://azure.github.io/enterprise-azure-policy-as-code/Images/epac-release-flow.png)

This flow works the same as the simplified version. With the biggest difference when you push your code to the main branch, the changes are implemented in a non-production environment. Whenever a branch is created with the `releases-prod/**`naming convention, the pipeline will trigger the production environment.

We are going to continue using the Simplified Github Flow for now. Perhaps in the future I will look into the implementation of the advanced release.

