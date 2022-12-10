# The Example System Archtecture

Laravel, Nuxt.js, MySQL, Redis, Kubernetes, on AWS Edition

The business problem/roadmap item is the need for a document and task manager. The following is what we know so far:

- The system shall allow a user to add documents
- The system shall version documents
- The system shall allow a user to add tasks to a document
- The system shall allow a user to download a document

Considerations:

- UI currently with design
- Product started user stories but needs to ensure technical solution before continuing
- Solution needs to consider how integrations with client's document management system will be impacted/ handled.
- This roadmap item is critical to a new client launch, which is expected to be completed by October.

Existing technology/infrastructure stack/skill of developers: 

- BE API (PHP / Laravel)
- FE (Nuxt / Vue.js)
- DB (MYSQL, REDIS)
- CLOUD (AWS, Kubernetes)
- CI/CD (Github actions / ArgoCD)

Please document what questions you have, assumptions you would make when coding, technical dependencies and draft a handful of technical stories. Along with the stories, provide a magnitude of effort so we can understand what we can do as a minimum for the October release. And lastly, a diagram if applicable to assist in explaining the above.

# Proof of Concepts

Before trying to connect different tools and technologies together, it is important to understand how they both function and can integrate. It is for that reason I like to generally standup small and self-contained example repositories for getting a better understanding. In this case, the following repositories represent each individual technology consideration:

- PHP / Laravel, which includes MySQL and Redis: https://github.com/jvalentino/example-docker-laravel
- Nuxt.js: https://github.com/jvalentino/example-nuxtjs
- Kuberentes: https://github.com/jvalentino/setup-kubernetes
- ArgoCD: https://github.com/jvalentino/setup-argocd

Consider that I build such examples based on other research and supporting projects that already exist, which in this case would be:

- Docker and Docker Compose: https://github.com/jvalentino/setup-docker
- Git: https://github.com/jvalentino/setup-git

# High-Level Architecture

This additionally includes questions and assumptions and points for guiding conversations.

## (1) Highest-Level System Needs

![01-mindmap](./diagrams/01-mindmap.png)

- The only two specific asks are for Document Management and Custom Client Integration
- Implicity we know we need a means of managing users, incombination with the infrastructure for hosting any web accessible solution

## (2) "Need" Dependencies

![02-dependencies](./diagrams/02-dependencies.png)

- Implementicy everyone of the high-level needs depends on something
- We can't pull client documents into our system, with the means of being able to manage documents
- We can't manage document without underyling associated accounts with appropriate authorization
- We can't do anytyhing of this without underlying infrastructure 

## (3) User Interface Perspective

![03-user-interface-pers](./diagrams/03-user-interface-pers.png)

- Users generally will only understand for that which they directly interact.
- Those points of interaction are always some soft of graphical user interface
- Now is a good time to ask what our interfaces are, as that will in a major way shape the scope of what we are building
- Additionally, the number of users intended to operate will also shape the underlying architure. Dealing with more than 10k concurrent users will require some specific web-scale strategies.

## (4) MVP

![04-mvp](./diagrams/04-mvp.png)

- We want to build the most minimal thing possible, and get it in the hards of the user
- So far looking at the needs, it is shaping up to be these 4 domains all running on top of the infrastructure domain

## (5) Architectural Design Principales

- Domain Driven Design
- Microservices
- High Availability
- Fault Tolernt
- Defense in Depath
- Backend for Frontend
- Cloud First
- Contract Driven Design (Swagger)

## (6) Contacts and Paths of Communication

![06-contract-comms](./diagrams/06-contract-comms.png)

- Encryption in flight is standard
- Only the services which much talk to the public internet are expozed to it (DMZ)
- Interval services use certificate based authorization
- Domaon boundaries are quantified by Swapper API specifications for REST

## (7) Domain: Web Portal

![05-web-portal-domain](./diagrams/05-web-portal-domain.png)

- A distributed cache is needed to maintain BFF session tokens, so that we dont' have to use sticky sessions
- BFF pattern is used for the Web Portal's Backend
- AWS secret manager is used for secret retrieval 
- ElasticCache is used as a Redis SaaS offering
- The Web BFF communicates with the other domains via REST per the appropriate Swagger API

## (8) The important of Dev/Test is isolation

![07-isolate-web-domain](./diagrams/07-isolate-web-domain.png)

- There are two basic needs for us to be able to work independently (without other domains or services being done), and to be able to test in isolation
- Wiremock can take in swagger specs to mock behavior.

## (9) User and Document Domains

![08-other-domains](./diagrams/08-other-domains.png)

- Both of these domains are a means of separating document related logic from user related logic
- While it is possible to maintain this in a single codebase, it can end up being harder to decouple after the fact
- Also consider that will likely be other domains in the future, so it is good to start the practice and prove it out

## (10) Domain: Custom Client Integration

![09-client-int-domain](./diagrams/09-client-int-domain.png)

- There are important unanswered questions that will fundemtnally shape this archicture
- An assumption was made to use OneDrive and its REST and Webhook API to show how this could look
- There are many other potential options

## (11) Domain: Infrastructure

![10-infrastructure-domain](./diagrams/10-infrastructure-domain.png)

- EKS based environmment, which requires a container registry, this ECR
- ArgoCD as a preference was aded for CLI based management via infrastructure as code
- New Relic is the recommendation solution as it is all-in-one, instead of having to combine many other thing together
- Separate explanation available at https://github.com/jvalentino/setup-argocd

## (12) Product Taxonomy

![11-taxonomy](./diagrams/11-taxonomy.png)

- Splits into what is typically dev and then what is typically ops, together as one team as DevOps
- Aligns domains to underlying software components

## (13) Feature and Activity Flow

![12-feature-flow](./diagrams/12-feature-flow.png)

- At a domain level, describes the high-level activity and features, with with their order of execution due to dependencies
- Add, Tasks, Version, Download, Download (Client), and Upload (Client) have been colored to show that they are the primary features.

## (14) Timeline: Probably, but too many unknowns

![13-timeline](./diagrams/13-timeline.png)

## (15) Understanding Uncertainty

![14-burndown](./diagrams/14-burndown.png)

## (16) Time = Scope

![15-time-scope](./diagrams/15-time-scope.png)

## (17) Scope Management

![15-scope-management](./diagrams/15-scope-management.png)

# Technical Examples

## Common Definition of Done

- Updates an existing automated test, or adds a new one
- The test automation exists at the lowest level possible
- Does not drop unit test coverage below the threshold of 75% line coverage
- Contains zero static code analysis issues
- Has been deployed to the highest appropriate environment 
- Has been peer reviewed
- Is validated as available via logging and monitoring

## Feature: Infrastructure

The intention of this feature is to establish the underlying runtime environments requiring for hosting this system from a custom application and third-party middleware perspective. This additionally includes the ability to maintain, monitor, and validate that content.

### Epic: [Infra] Platform

Prerequisites

- None

The purpose of this epic is to handle the creation of the underlying application contain runtime environment. Specifically this is EKS, which means Kubernetes on AWS, whereby all setup and maintance is via CloudFormation.

Epic-Level Acceptance Criteria

- Environment Strategy - The intention is to confirm the environment stategy.
- ECR CloudFormation - We need to esbtablish a container registry using ECR, which will will be done via its own repository  using CloudFormation. This is needed in order to store the container images that will be run on EKS.
- EKS Namespaces - Via CloudFormation on a new repository, we want to estbablish a new k8s cluster using a namespace for each environment.
- ArgoCD Installation - Via the EKS cloudFormation repository, we want to install the current version of ArgoCD. This will be used to manage deployment and configurations via the ArgoCD CLI within Gitub Actions for the pirpose of deployment pipelines.

#### Story: [Infra] [Platform] Environment Strategy

Prerequisites

- None

The intention is to confirm the environment stategy. The current proposed environment strategy at the individual application repository level is that:

1. Developers do whatever work they need to locally, using wiremock where applicable to mock any dependent services, and otherwise host what they need locally in terms of supporting infrastructure using Docker.
2. When code is commited (regardless of branch), it initiates the standard CI pipeline.
3. When code is commited to the master/main branch, it goes through the standard CI pipeline, but upon completion publishes a new container image for the application to ECR as a new version, and then additionally tirggers DEV deployment.
4. Deployment to DEV additionally includes integration testing to ensure an app is working with its connection points.
5. The purpose of the DEV environment is the first place where all different domains are able to integrate.
6. Upon successly deployment and integration testing in the DEV environment, the env-DEV tag is updated/create on the underlying reposistory to show that version (from ECR) that has been deployed there.
7. Nightly or upon manual runtime, a pipeline for STAGE deployment is run which looks at what last when to DEV, and deploys that container version to the STAGE environment. At a minimum this includes a smoke test to ensure it came on, and when applicable automated journey testing. 
8. Upon successly deployment and  testing in the STAGE environment, the env-STAGE tag is updated/create on the underlying reposistory to show that version (from ECR) that has been deployed there.
9. Upon manual runtime, a pipeline for PROD deployment is run which looks at what last when to STAGE, and deploys that container version to the PROD environment. At a minimum this includes a smoke test to ensure it came on.
10. Upon successly deployment and  testing in the PROD environment, the env-PROD tag is updated/create on the underlying reposistory to show that version (from ECR) that has been deployed there.

Acceptance Criteria

- That there is consensus on the environment strategy, or that it is changed in order to reach that consensus.

#### Story: [Infra] [Platform] ECR CloudFormation

Prerequsites

- Story: [Infra] [Platform] Environment Strategy

References:

- https://docs.aws.amazon.com/AWSCloudFormation/latest/UserGuide/aws-resource-ecr-repository.html#aws-resource-ecr-repository--examples
- https://docs.aws.amazon.com/AWSCloudFormation/latest/UserGuide/sdk-general-information-section.html
- https://towardsaws.com/build-push-docker-image-to-aws-ecr-using-github-actions-8396888a8f9e

We need to esbtablish a container registry using ECR, which will will be done via its own repository  using CloudFormation. This is needed in order to store the container images that will be run on EKS. The inteintion is to both create and maintain this ECR via config as code and pipelines, so that it isn't something that requires continually command-line level manual intervention.

Acceptance Criteria

- Creation of a new source code repository with the intention of containing the CloudFormation settings for setting up and otherwise  maintaining a new ECR.
- The creation of a build automation in an agreed upon technology stack, which is able to use the CloudFormation SDK to create the ECR
- The execution of that build automation upon code change, using GitHub Actions.
- Demonstration using a "Hello World" style project as its own repository, that pushes a new image version to that ECR where it is then available.

# References

## Distributed Caching

### Amazon ElastiCache

> Amazon ElastiCache is a fully managed in-memory data store and cache service by Amazon Web Services (AWS). The service improves the performance of web applications by retrieving information from managed in-memory caches, instead of relying entirely on slower disk-based databases. ElastiCache supports two open-source in-memory caching engines: Memcached and Redis (also called "ElastiCache for Redis").[2]

- https://en.wikipedia.org/wiki/Amazon_ElastiCache

### Amazon ElastiCache for Redis

> Welcome to the *Amazon ElastiCache for Redis User Guide*. Amazon ElastiCache is a web service that makes it easy to set up, manage, and scale a distributed in-memory data store or cache environment in the cloud. It provides a high-performance, scalable, and cost-effective caching solution. At the same time, it helps remove the complexity associated with deploying and managing a distributed cache environment.

- https://docs.aws.amazon.com/AmazonElastiCache/latest/red-ug/WhatIs.html

## Data

### RDS for MySQL

> **Amazon Relational Database Service** (or **Amazon RDS**) is a distributed [relational database](https://en.wikipedia.org/wiki/Relational_database) service by [Amazon Web Services](https://en.wikipedia.org/wiki/Amazon_Web_Services) (AWS).[[2\]](https://en.wikipedia.org/wiki/Amazon_Relational_Database_Service#cite_note-2) It is a [web service](https://en.wikipedia.org/wiki/Web_service) running ["in the cloud"](https://en.wikipedia.org/wiki/Cloud_computing) designed to simplify the setup, operation, and [scaling](https://en.wikipedia.org/wiki/Scalability) of a relational database for use in applications.[[3\]](https://en.wikipedia.org/wiki/Amazon_Relational_Database_Service#cite_note-3) Administration processes like patching the database software, backing up databases and enabling [point-in-time recovery](https://en.wikipedia.org/wiki/Point-in-time_recovery) are managed automatically.[[4\]](https://en.wikipedia.org/wiki/Amazon_Relational_Database_Service#cite_note-4) Scaling storage and compute resources can be performed by a single [API](https://en.wikipedia.org/wiki/API) call to the AWS control plane on-demand. AWS does not offer an [SSH](https://en.wikipedia.org/wiki/Secure_Shell) connection to the underlying virtual machine as part of the managed service.[[5\]](https://en.wikipedia.org/wiki/Amazon_Relational_Database_Service#cite_note-5)

- https://en.wikipedia.org/wiki/Amazon_Relational_Database_Service

> [MySQL](https://aws.amazon.com/rds/mysql/what-is-mysql/) is the world's most popular [open source](https://aws.amazon.com/products/databases/open-source-databases/) relational database and Amazon RDS makes it easer to set up, operate, and scale MySQL deployments in [the cloud](https://aws.amazon.com/what-is-cloud-computing/). With Amazon RDS, you can deploy scalable MySQL servers in minutes with cost-efficient and resizable hardware capacity.
>
> Amazon RDS for MySQL frees you up to focus on application development by managing time-consuming database administration tasks, including backups, upgrades, software patching, performance improvements, monitoring, scaling, and replication.
>
> Amazon RDS supports MySQL Community Edition versions 5.7 and 8.0 which means that the code, applications, and tools you already use today can be used with Amazon RDS.

- https://aws.amazon.com/rds/mysql/

## API

### Swagger

> Swagger was created by the team behind the original “Swagger Specification”, which has since been renamed to the OpenAPI Specification. Today, Swagger has evolved into one of the most widely used open source tool sets for developing APIs with rich support for the OpenAPI Specification, AsyncAPI specification, JSON Schema and more.

- https://swagger.io/tools/open-source/

### Wiremock

A mock API’s stubs can be exported in bulk via the admin API. This can be useful for backing up your API to source control, or cloning the contents of one API into another.

Importing

To import any of the supported formats (Swagger, OpenAPI, Postman, WireMock JSON), execute a `POST` request to the stub import URL e.g.:

```
curl -v \
  --data-binary @my-swagger-spec.yaml \
  http://localhost:8000/__admin/mocklab/imports
```

Exporting in WireMock JSON format

To export an API’s stubs, execute a `GET` request to the stub mappings admin URL e.g.:

```
curl --output my-stubs.json \
  http://localhost:8000/__admin/mappings
```

Reference: https://wiremock.org/studio/docs/import-export/api/

## Security

### TLS Mutual Auth on NGinx

Explains hwo to configure an nginx web server with the two-certificate setup.

- https://smallstep.com/hello-mtls/doc/server/nginx

### AWS Secrets Manager SDK

> The AWS SDKs consist of libraries and sample code for various programming languages and platforms, for example, Java, Python, Ruby, .NET, and others. The SDKs include tasks such as cryptographically signing requests, managing errors, and retrying requests automatically. For more information, see AWS SDKs.
>
> To download and install any of the SDKs, see [Tools for Amazon Web Services](https://aws.amazon.com/tools/#sdk).
>
> For SDK documentation, see:
>
> - [C++](http://sdk.amazonaws.com/cpp/api/LATEST/namespace_aws_1_1_secrets_manager.html)
> - [Java](https://docs.aws.amazon.com/AWSJavaSDK/latest/javadoc/com/amazonaws/services/secretsmanager/package-summary.html)
> - [PHP](https://docs.aws.amazon.com//aws-sdk-php/v3/api/namespace-Aws.SecretsManager.html)
> - [Python](https://boto3.amazonaws.com/v1/documentation/api/latest/reference/services/secretsmanager.html)
> - [Ruby](https://docs.aws.amazon.com/sdk-for-ruby/v3/api/Aws/SecretsManager.html)
> - [.NET](https://docs.aws.amazon.com/sdkfornet/v3/apidocs/items/SecretsManager/NSecretsManagerModel.html)
> - [Node.js](https://docs.aws.amazon.com/AWSJavaScriptSDK/latest/AWS/SecretsManager.html)
> - [Go](https://docs.aws.amazon.com/sdk-for-go/api/service/secretsmanager/)

- https://docs.aws.amazon.com/secretsmanager/latest/userguide/asm_access.html

## Platform

### EKS CloudFormation

> Creates an Amazon EKS control plane.
>
> The Amazon EKS control plane consists of control plane instances that run the Kubernetes software, such as `etcd` and the API server. The control plane runs in an account managed by AWS, and the Kubernetes API is exposed by the Amazon EKS API server endpoint. Each Amazon EKS cluster control plane is single tenant and unique. It runs on its own set of Amazon EC2 instances.
>
> The cluster control plane is provisioned across multiple Availability Zones and fronted by an Elastic Load Balancing Network Load Balancer. Amazon EKS also provisions elastic network interfaces in your VPC subnets to provide connectivity from the control plane instances to the nodes (for example, to support `kubectl exec`, `logs`, and `proxy` data flows).
>
> Amazon EKS nodes run in your AWS account and connect to your cluster's control plane over the Kubernetes API server endpoint and a certificate file that is created for your cluster.

- https://docs.aws.amazon.com/AWSCloudFormation/latest/UserGuide/aws-resource-eks-cluster.html