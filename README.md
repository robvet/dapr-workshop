# Dapr hands-on

This repository contains several hands-on assignments that will introduce you to Dapr. You will start with a simple ASP.NET Core application that several microservices. In each assignment, you will change a part of the application so it works with Dapr (or "rub some Dapr on it" as Donovan Brown would say). You will be working with the following Dapr building blocks:

- Service invocation
- State-management
- Publish / Subscribe
- Bindings
- Secrets management

As Dapr can run on a variety of hosts, for this lab, you will be use Dapr in *self-hosted mode*.

## The domain

The assignments implement a traffic-control camera system that are found on several Dutch highways. Here's how the simulation works:

![Speeding cameras](img/speed-trap-overview.png)

There's 1 entry-camera and 1 exit-camera per lane. When a car passes an entry-camera, a photo of license plate is taken and the car and the timestamp is registered.

When the car passes an exit-camera, another photo and  timestamp are registered. The system then calculates the average speed of the car based on the entry- and exit-timestamp. If a speeding violation is detected, a message is sent to the Central Fine Collection Agency (or CJIB in Dutch). The system retrieves the vehicle information and the vehicle owner is sent a fine.

### Architecture

The traffic-control application architecture consists of five microservices:

![Services](img/services.png)

These services work together to simulate a traffic control scenario in code.

The `src` folder in the repo contains the starting-point for the workshop. It contains a version of the services that use plain HTTP communication and store state in memory. With each assignment of the workshop, you will add a Dapr building block to the solution.

- The **Camera Simulation** is a .NET Core console application that will simulate passing cars.
- The **Traffic Control Service** is an ASP.NET Core WebAPI application that offers 2 endpoints: `/entrycam` and `/exitcam`.
- The **Fine Collection Service** is an ASP.NET Core WebAPI application that offers 1 endpoint: `/collectfine` for collecting fines.
- The **Vehicle Registration Service** is an ASP.NET Core WebAPI application that offers 1 endpoint: `/getvehicleinfo/{license-number}` for getting the vehicle- and owner-information of a vehicle.

The way the simulation works is depicted in the sequence diagram below:

<img src="img/sequence.png" alt="Sequence diagram" style="zoom:67%;" />

1. The Camera Simulation generates a random license-number and sends a *VehicleRegistered* message (containing this license-number, a random entry-lane (1-3) and the timestamp) to the `/entrycam` endpoint of the TrafficControlService.
1. The TrafficControlService stores the *VehicleState* (license-number and entry-timestamp).
1. After some random interval, the Camera Simulation sends a *VehicleRegistered* message to the `/exitcam` endpoint of the TrafficControlService (containing the license-number generated in step 1, a random exit-lane (1-3) and the exit timestamp).
1. The TrafficControlService retrieves the *VehicleState* that was stored at vehicle entry.
1. The TrafficControlService calculates the average speed of the vehicle using the entry- and exit-timestamp. It also stores the *VehicleState* with the exit timestamp for audit purposes, which is left out of the sequence diagram for clarity.
1. If the average speed is above the speed-limit, the TrafficControlService calls the `/collectfine` endpoint of the FineCollectionService. The request payload will be a *SpeedingViolation* containing the license-number of the vehicle, the identifier of the road, the speeding-violation in KMh and the timestamp of the violation.
1. The FineCollectionService calculates the fine for the speeding-violation.
1. The FineCollectionSerivice calls the `/vehicleinfo/{license-number}` endpoint of the VehicleRegistrationService with the license-number of the speeding vehicle to retrieve its vehicle- and owner-information.
1. The FineCollectionService sends a fine to the owner of the vehicle by email.

All actions described in this sequence are logged to the console during execution so you can follow the flow.

It's important to understand that all calls between services are direct, synchronous HTTP calls using the HttpClient plumbing in .NET Core. While sometimes necessary, this type of synchronous communication is **NOT** considered a best practice for distributed microservice applications. When possible, you should consider decoupling microservices using asynchronous messaging. However, decoupling communication can dramatically increase the architectural and operational complexity of an application. You'll soon see how Dapr reduces the inherent complexity of distributed microservice applications.

### End-state with Dapr applied

Completing the lab assignments, you will evolve the application architecture to work with Dapr. The following diagram shows the end-state:

<img src="img/dapr-setup.png" alt="Dapr setup" style="zoom:67%;" />

1. For request/response type communication between the FineCollectionService and the VehicleRegistrationService, the **service invocation** building block is used.
1. For sending speeding violations to the FineCollectionService, the **publish and subscribe** building block is used. RabbitMQ is used as message broker.
1. For storing the state of a vehicle, the **state management** building block is used. Redis is used as state store.
1. Fines are sent to the owner of a speeding vehicle by email. For sending the email, the Dapr SMTP **output binding** is used.
1. The Dapr **input binding** for MQTT is used to send simulated car info to the TrafficControlService. Mosquitto is used as MQTT broker.
1. The FineCollectionService needs credentials for connecting to the smtp server and a license key for a fine calculator component. It uses the **secrets management** building block with the local file component to get the credentials and the license key.

The sequence diagram below shows how the solution will work with Dapr:

<img src="img/sequence-dapr.png" alt="Sequence diagram with Dapr" style="zoom:67%;" />

> If during the workshop you are lost on what the end result of an assignment should be, come back to this README to see the end result.

## Getting started with the workshop

### Prerequisites

Make sure you have the following prerequisites installed on your machine:

- Git ([download](https://git-scm.com/))
- .NET 5 SDK ([download](https://dotnet.microsoft.com/download/dotnet/5.0))
- Visual Studio Code ([download](https://code.visualstudio.com/download)) with at least the following extensions installed:
  - [C#](https://marketplace.visualstudio.com/items?itemName=ms-dotnettools.csharp)
  - [REST Client](https://marketplace.visualstudio.com/items?itemName=humao.rest-client)
- Docker for desktop ([download](https://www.docker.com/products/docker-desktop))
- Dapr CLI and Dapr runtime ([instructions](https://docs.dapr.io/getting-started/install-dapr-selfhost/))

All scripts in the instructions are Powershell scripts. If you're working on a Mac, it is recommended to install Powershell for Mac:

- Powershell for Mac ([instructions](https://docs.microsoft.com/nl-nl/powershell/scripting/install/installing-powershell-core-on-macos?view=powershell-7.1))

### Versions

The workshop has been tested with the following versions:

| Attribute            | Details |
| -------------------- | ------- |
| Dapr runtime version | v1.0.0  |
| Dapr.NET SDK version | v1.0.0  |
| Dapr CLI version     | v1.0.0  |
| Platform             | .NET 5  |

### Instructions

Every assignment is contained in a separate folder in this repo. Each folder contains the description of the assignment that you can follow.

**It is important you work through all the assignments in order and don't skip any assignments. The instructions for each subsequent assignment rely on the fact that you have finished the previous assignments successfully.**

The `src` folder in the repo contains the starting-point for the workshop. It contains a version of the services that use plain HTTP communication and stores state in memory. With each assignment of the workshop, you will add a Dapr building block to this solution.

The description for each assignment (accept the first one) contains *two approaches* for completing the tasks: A **DIY** option or a **step-by-step** option. The DIY option states the outcome you need to achieve and provides no further instruction. It's entirely up to you to achieve the goals with the help of the Dapr documentation. The step-by-step option describes exactly what you need to change in the application step-by-step. It's up to you to pick an approach. If you pick the DIY approach and get stuck, you can always go to the step-by-step approach for some help.

#### Integrated terminal

During the workshop, you should be working in 1 instance of VS Code. You will use the integrated terminal in VS Code extensively. All terminal commands have been tested on a Windows machine with the integrated Powershell terminal in VS Code. If you have any issues with the commands on Linux or Mac, please create an issue or a PR to add the appropriate command.

 > Optionally, you may want to install and open the free [Typora](https://typora.io/) markdown application to read the lab instructions and use VS Code for your development work.

#### Prevent port collisions

During the workshop you will run the services in the solution on your local machine. To prevent port-collisions, all services listen on a different HTTP port. When running the services with Dapr, you need additional ports for HTTP and gRPC communication with the sidecars. By default, these ports are `3500` and `50001`. But, to prevent port collisions, you'll use different port numbers in the assignments. Please closely follow the instructions so that your microservices use the following ports for their Dapr sidecars:

| Service                    | Application Port | Dapr sidecar HTTP port | Dapr sidecar gRPC port |
| -------------------------- | ---------------- | ---------------------- | ---------------------- |
| TrafficControlService      | 6000             | 3600                   | 60000                  |
| FineCollectionService      | 6001             | 3601                   | 60001                  |
| VehicleRegistrationService | 6002             | 3602                   | 60002                  |

Use the ports specified in the above table *whether* using the DIY or step-by-step approach. 

You will specify the ports on the command-line when starting a service with the Dapr CLI. The following command-line flags will be used:

- `--app-port`
- `--dapr-http-port`
- `--dapr-grpc-port`

#### Kudos to the originators

Before we start, please give a big hand to original authors of this workshop:

<img src="img/edwin.png" style="zoom:67%;" />

<img src="img/sander.png" style="zoom:67%;" />

Both Edwin and Sander are Principal Architects at InfoSupport in the Netherlands. Both are Microsoft MVPs, avid community presenters, and co-authors of the Microsoft eBook [Dapr for .NET Developers](https://docs.microsoft.com/dotnet/architecture/dapr-for-net-developers/). 


#### Get started

Now it's time for you to get your hands dirty and start with the first assignment:

1. Clone the Github repository to a local folder on your machine:

   ```console
   git clone https://github.com/robvet/dapr-workshop.git
   ```

2. Before starting with the assignments, I suggest you review  the code of the different services. You can open the `src` folder in this repo in VS Code. All folders used in the assignments are specified relative to the root of the folder where you have cloned the dapr-workshop repository.

3. Go to [assignment 1](Assignment01/README.md).
