# Assignment 2 - Add Dapr service invocation

In this assignment, you're going to add Dapr into the mix. You will use the Dapr **service invocation** building block.

## Dapr service invocation building block

While decoupled, asynchronous communication is favored, some microservice operations require an immediate response. In these cases, calls between services are often synchronously implemented.

In these scenarios, it's important for a service to communicate with other services without "hardcoding" their location. This is especially important in orchestrated environments, such as Kubernetes, where services are continually moved across cluster nodes and replaced with newer version. The Dapr service invocation building block addresses service-to-service communication. Here is how it works:

<img src="img/service-invocation.png" style="zoom: 33%;" />

In Dapr, every service is started with a unique Id (the *app-id*) which can be used the find it. What happens if Service A needs to call Service B?

1. Service A invokes the Dapr service invocation API (using HTTP or gRPC) on its Dapr sidecar and specifies the unique app-id of Service B.
1. Dapr discovers Service B's current location by using the name-resolution component for the hosting environment in which the solution is running.
1. Service A's Dapr sidecar forwards the message to Service B's Dapr sidecar.
1. Service B's Dapr sidecar forwards the request to Service B.  Service B performs its corresponding business logic.
1. Service B returns a response for Service A to its Dapr sidecar.
1. Service B's Dapr sidecar forwards the response to Service A's Dapr sidecar.
1. Service A's Dapr sidecar forwards the response to Service A.

> The building block offers many other features such as security and load-balancing. Check out the Dapr documentation later to learn all about them.

For this hands-on assignment, you will decouple communication between two services.

## Assignment goals

To complete this assignment, you must achieve the following goals:

- The VehicleRegistrationService and FineCollectionService must each run with a Dapr sidecar.
- The FineCollectionService must use the Dapr service invocation building block to call the `/vehicleinfo/{licensenumber}` endpoint on the VehicleRegistrationService.

This assignment targets the operations labeled as **number 1** in the end-state setup:

<img src="../img/dapr-setup.png" style="zoom: 67%;" />

There are two ways to approach this assignment: DIY (do it yourself) or with step-by-step instructions.

## DIY instructions

Open the `src` folder in this repo in VS Code. Then open the [Dapr service invocation documentation](https://docs.dapr.io/developing-applications/building-blocks/service-invocation/) and start hacking away. If you need any hints, you may peek in the step-by-step approach.

## Step by step instructions

To leverage step-by-step instructions to achieve the goals, open the [step-by-step instructions](step-by-step.md).

## Next assignment

Make sure you stop all running processes and close all the terminal windows in VS Code before proceeding to the next assignment.

Go to [assignment 3](../Assignment03/README.md).
