# Assignment 6 - Add a Dapr input binding

In this assignment, you're going to add a Dapr **input binding** in the TrafficControlService. It'll receive entry- and exit-cam messages over the MQTT protocol.

## Dapr bindings

In this assignment you'll focus on Dapr input bindings. The following diagram depicts how input bindings work:

<img src="img/input-binding.png" style="zoom: 50%;padding-top: 40px;" />

For this hands-on assignment, you will add an input binding leveraging the Dapr binding building block. In the previous assignment, you implemented a Dapr input binding. For detailed information, read the [introduction to this building block](https://docs.dapr.io/developing-applications/building-blocks/bindings/) in the Dapr documentation and the [bindings chapter](https://docs.microsoft.com/dotnet/architecture/dapr-for-net-developers/bindings) in the [Dapr for .NET developers](https://docs.microsoft.com/dotnet/architecture/dapr-for-net-developers/) guidance eBook.


## Assignment goals

To complete this assignment, you must reach the following goals:

- The TrafficControlService uses the Dapr MQTT input binding to receive entry- and exit-cam messages over the MQTT protocol.
- The MQTT binding uses the lightweight MQTT message broker Mosquitto that runs as part of the solution in a Docker container.
- The Camera Simulation publishes entry- and exit-cam messages to the MQTT broker.

This assignment targets the operation labeled as **number 5** in the end-state setup:

<img src="../img/dapr-setup.png" style="zoom: 67%;padding-top: 25px;" />

## DIY instructions

First open the `src` folder in this repo in VS Code. Then open the [Bindings documentation](https://docs.dapr.io/developing-applications/building-blocks/bindings/) and start hacking away. As MQTT broker, you can use the lightweight MQTT broker [Mosquitto](https://mosquitto.org/).

## Step by step instructions

To get step-by-step instructions to achieve the goals, open the [step-by-step instructions](step-by-step.md).

## Next assignment

Congratulations! You have now completed assignment 6.

Make sure you stop all running processes and close all the terminal windows in VS Code before proceeding to the next assignment.

Go to [assignment 7](../Assignment07/README.md).
