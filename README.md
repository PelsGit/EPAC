# EPAC Implementation with Github Pipelines
Enterprise Policy As Code https://azure.github.io/enterprise-azure-policy-as-code/


## Introduction

Azure policy is a very powerful way of deploying [guardrails] (https://learn.microsoft.com/en-us/azure/cloud-adoption-framework/ready/enterprise-scale/dine-guidance) for your organization. For years I have worked with customers understanding the need for having guardrails and what on the longer term the impact it can have on the Azure experience. 

Ensuring guardrails might be a fairly easy concept to understand, to master it requires a bit more finese and mastery of the technical skills. I am often asked:

- What policies do I need to use?
- How can I manage these policies?
- How can I automate the lifecycle managent of Policies?
- How can I prevent production outtages from changes to (built-in) policies?

All good questions. Although it has always been feasible to master the questions above, it did require some in-depth knowledge on Azure Policies, technical skills on Pipelining and scripting to achieve the above solutions. 

When I heard of the Enterprise Policy As Code initiative by Microsoft, I was immediatly excited to give it a whirl. 
Besides wanting to understand the value it can add for my customers, I wanted to know if I can use the assets as an out-of-the-box solution for my customers. 

This post is a guide where I attempt to deploy it using the assets from the repo, explain what it does in a certain degree, and give my honest opinion on the experience of using.

## Preparation

