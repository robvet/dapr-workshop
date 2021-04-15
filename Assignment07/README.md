# Assignment 7 - Add secrets management

In this assignment, you're going to add the Dapr **secrets management** building block.

## Dapr secrets management building block

Almost all non-trivial applications need to _securely_ store secret data like API keys, database passwords, and more. As a best practice, secrets should never be checked into the version control system. But, as the same time,they need to be accessible to code running in production. This is generally a challenging requirement, but critical to get right.

Dapr provides a solution to this problem: The Dapr secrets building block. It includes an API and a secrets store.

Here's how it works:

- Dapr exposes a **secret store** - a place to securely store secret data.
- Dapr provides an API that enables applications to retrieve secrets from the store.

Popular secret stores include `Kubernetes`, `Hashicorp Vault`, and `Azure KeyVault`.

The following diagram depicts an application requesting the secret called "mysecret" from a secret store called "vault" from a configured cloud secret store:

<img src="img/secrets_cloud_stores.png" style="zoom:67%;" />

Note the blue-colored Dapr secrets building block that sits between the application and secret stores.

> For this assignment you'll use a file-based local secret store component. Local stores are meant for development or testing purposes. Never use them in production!

Another way of using secrets, is to reference them from Dapr configuration files. You will use both approaches in this assignment.

To learn more about the secrets building block, read the [introduction to this building block](https://docs.dapr.io/developing-applications/building-blocks/secrets/) in the Dapr documentation. Also, checkout the [secrets chapter](https://docs.microsoft.com/dotnet/architecture/dapr-for-net-developers/secrets) in the [Dapr for .NET Developers](https://docs.microsoft.com/dotnet/architecture/dapr-for-net-developers/) guidance eBook.

## Assignment goals

To complete this assignment, you must reach the following goals:

- The credentials used by the SMTP output binding to connect to the SMTP server are retrieved using the Dapr secrets management building block.
- The FineCollectionService retrieves the license key for the `FineCalculator` component it uses from the Dapr secrets management building block.

This assignment targets the operation labeled as **number 6** in the end-state setup:

<img src="../img/dapr-setup.png" style="zoom: 67%;" />

## DIY instructions

First open the `src` folder in this repo in VS Code. Then open the [Secrets management documentation](https://docs.dapr.io/developing-applications/building-blocks/secrets/) and start hacking away.

## Step by step instructions

To get step-by-step instructions to achieve the goals, open the [step-by-step instructions](step-by-step.md).

## Final solution

Congratulations, you have reached the end of the hands-on assignments! If you haven't been able to do all the assignments, go to [this repository](https://github.com/robvet/dapr-workshop-complete) for the end result.

Thanks for participating in these hands-on assignments! Hopefully you've learned about Dapr and how to use it. Obviously, these assignments barely scratch the surface of what is possible with Dapr. We have not touched upon subjects like: hardening production environments, actors, integration with Azure Functions, Azure API Management and Azure Logic Apps just to name a few. So if you're interested in learning more, I suggest you read the [Dapr documentation](https://docs.dapr.io).
