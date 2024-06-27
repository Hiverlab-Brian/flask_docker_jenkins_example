# flask-docker-jenkins example

This repository provides a simple template for creating unit tests for a basic Flask app using the pytest module. The unit tests can target both web pages returned by your web app and responses from API endpoints. The web app runs on port 80, so after running the code, you can access it at http://localhost or http://localhost:80.

## Overview
This example is a Dockerized project featuring:
- A single route for the home page that returns HTML.
- An API endpoint.
- A unit test configuration.
A Jenkins pipeline accompanies this project to facilitate continuous integration and continuous deployment (CI/CD).

## Table of Contents

- [Prerequisites](#prerequisites)
- [Jenkins Configuration](#jenkins-configuration)
- [Docker Configuration](#docker-configuration)
- [Documentation](#documentation)
- [Authors](#authors)

## Prerequisites

Ensure you have the following software tools installed:

### Docker

**Installation:** Head to the [Install Docker Engine](https://www.docker.com/get-started) page. \
**Learning:** If Docker is unfamiliar, consider this [quick introduction](https://docs.docker.com/get-started/overview/).

### Jenkins

**Installation:** 
 Reference the respective installation guides to setup in local environment
- Windows: [Service guide](https://www.jenkins.io/doc/book/installing/windows/)
- MacOS: [Service guide](https://www.jenkins.io/doc/book/installing/macos/)
- Linux: [Service guide](https://www.jenkins.io/doc/book/installing/linux/)

#### Terraform + Azure VM environment: reference this [guide](https://docs.google.com/document/d/1V13yVvlGjnnr_MeRa6ydn9yeDXzCmYuBqHHFOziYqHw/edit#heading=h.hnstmjjlgnbw)

## Jenkins Configuration

To configure your Jenkinsfile include the following stages, customizing each of them to suit your project's specific requirements.

### Checkout Stage
This stage handles checking out the code from version control.

#### Configuration:
- Use the Pipeline Syntax generator to select "Check out from version control".
- Configure the Git SCM section as done previously.
- Generate the pipeline script.

### Build Stage
This stage builds and runs the project to ensure it functions correctly with new commits.

#### Configuration:
- Add a build step to compile and run the project.
- Verify that the application starts without errors.

### Test Stage
This stage involves running various tests to ensure the project is fully tested.

#### Configuration:
- Implement unit tests, integration tests, and any other testing methods used in the project.
- Include verification methods to validate test outcomes.

### Deploy Stage
This stage handles the deployment of the project, fulfilling the Continuous Deployment (CD) process.

### (Optional) Post Block
The post block is used to handle actions that should be taken after the pipeline execution. It can include three common conditions: `always{}`, `success{}`, and `failure{}`.

#### Configuration:
- Deploy using Nginx for application deployment or pull from Docker Hub.
- Customize deployment steps according to user specifications.

## Docker Configuration
To customize this Docker Compose file for your specific needs, consider the following modifications:

- Ports: To change the ports on which services are accessible, modify the numbers in the ports settings for each service. Ensure the first number (host port) is available on your system.
- Volumes: If you wish to store data in different directories on your host, change the device paths under volumes.
- Build context and Dockerfile: The build directive in the web service assumes you have a Dockerfile in the current directory. If your Dockerfile is elsewhere, modify the path accordingly.
- Network names: The default network is named 3DBluePrint_fastapi. You can rename it under networks to better reflect your project or environment.

## Documentation
[Documentation (Jenkins pipeline configuration)](https://docs.google.com/document/d/18jhBCr1P3rfVli93_WenAmBiI23sLtZAj888cvLevPE/edit)

## Authors

* Brian Lim (AI/Backend Engineer Intern)
