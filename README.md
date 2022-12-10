Priority Operation Processing (POP)

How does Priority Operation Processing work?

* A framework for orchestrating workflows that schedules the workflow as a whole and gives resources priority once they are chosen 

* Customizable scheduling algorithms and priority queues 

Considering the needs of several tenants 

* Using AWS Authorizer for authentication 

* Access to data objects for authorized clients 

The execution flow is defined by a blueprint built on a JSON DAG.
* **Executes**** operations within a workflow by launching Kubernetes Pods as needed (dynamic resource allocation)

![images/pop-design.png](https://github.com/Comcast/Priority-Operation-Processing/wiki/images/pop-design.png)

Technologies
------------

*   API Gateway

*   AWS Authorizer

*   Lambda

*   CloudFormation

*   CloudWatch

*   Kubernetes

*   Kubernetes Annotations

*   Docker

*   Graphite



Components
==================

*Step 1: Submission*
-----------------
Workflow (Agenda)
POP's workflow data model is called an Agenda

### Features
* A short selection of procedures 

* Model for nodes in a graph or DAG 

* Interoperation variable referencing 

* Non-strict API for each operation's inputs and outputs 

* Decoupling of the agenda from the execution technologies and APIs. Each action has a JSON payload that complies with the POP Handler's API and is used to carry out the operation. 

As long as the dependency variables are satisfied, the Agenda Executor will carry out an operation right away. 

* The Agenda Executor will search for a Docker image based on the type of operation and launch a new Kubernetes Pod for it.


*Step 2: Scheduling*
----------
*Scheduling *

The Priority Operation Processing (POP) Brain is in charge of workflows for client callbacks, authentication, data submission (CRUD), data storage, and scheduling.

*Features* 

1. Visibility of Authorized Customer Data 

2. Endpoints of API 

  - Agenda 

  - Progress 

  - Pool of Resources 

      - Insights 
  - Customer 

3. Scheduling 

4. Data Management 

5. Alerts and Metrics 

6. linkId for tying all Data Objects together 

7. Agenda revisions 

  - Configurable 

  - At the failure point, try again
 
*Technologies* 

1. HTTP Gateway 

2. AWS Authorizer 

3. Lambda 

4. SQS 

5. Dynamo 

6. CloudFormation 

7. CloudWatch

*Tying an agenda to a processing insight or queue *

1. Based on a set of agenda insights and queue specifications, a ResourcePool (a Kubernetes farm that consumes work) will determine the kind of work it can take on. 

2. Each Insight / Queue's pending task will be scheduled by Priority Operation Processing (POP). 

*Pool of Resources *

A puller to gather resources and labour to process (typically a Kubernetes farm) 

*Insight: *

A description of the work type in the queue 

*Scheduling procedures:*

1. Assemble the following clients waiting in line with work for each Resource Pool's insight. 

2. Add a list of clients to the proper insight-defined queue 

3. Keep an eye out for job fatigue and the refill line

*How does it function? *

*Updating the agenda (customer) *

1. A ready-to-run Agenda or a payload with an AgendaTemplate.id is sent by the customer. 

2. The agenda is linked to a Resource Pool insight. 

3. With the AgendaId, CustomerId, InsightId, and a state of available, a new Ready Agenda entry is produced. 

4. The Agenda is seen as available and unoccupied. 

*Scheduling (background processing)*

1. Queues are watched for low or empty item counts. 

2. Which clients with products on the Ready Agenda will be given the opportunity to queue is decided upon fairly. 

3. The customer's insight and the filter are used to determine which agendas should be queued for the customer.

4. All chosen items are placed in a queue (within the queue's parameters and fairly). 

5. The Ready Agenda items are moved to the queue. 

*Obtaining the accessible agendas (puller) *

1. Puller requests insightId work 

2. The following Agenda is returned for that insight (or more if more were requested)

*Step 3: Execution*
-------------------------

Processing of Operation with Priority: ResourcePool API 

*Overview *

A Kubernetes cluster called a Resource Pool pulls for complete processes known as Agendas. One or more queues that a Resource Pool draws from are filtered by insights.

*Features *

*Agenda Puller* 

1. The puller is a Kubernetes pod that runs and uses the Agenda Service to pull for work. getAgenda 

2. The creation of an on-demand Kubernetes pod for agenda execution 

*Agenda Executor *

1. Executes all operations in the agenda by dynamically spinning up resources. 

2. Metrics and Alerts 

3. Progress updates 

4. Concurrent processing 

5. Processing of Node Graph / DAG models 

*Handler *

1. One-time Agenda Operation Executor 

2. Metrics and Alerts

3. An API owner 

4. Progress updates 

*Technologies *

1. Kubernetes 

2. Observables in Kubernetes 

3. Docker 

4. Bananas 

5. Graphite

*Technical Solutions*

*Scheduling *

1. Supports several work queue scheduling techniques (FIFO, Fairness, Priority) 

2. Queues are scheduled asynchronously, eliminating the need for complex calculations when work is required. 

3. Insights (keywords, IDs, and key-value pairs) define queues. 

4. Queue sizes can be changed. 

*Support for new technologies that plugs in *

1. Pods are spun up dynamically using Docker images. 

2. For new POP Handlers, there is no mandatory schema registration. 

3. Zoning issues absent 

4. Each handler has no account permissions. 

5. Simple Handler configuration for Docker images 

*Bare-metal, Cloud, and Scalability *

1. Kubernetes 

2. Each POP Handler performs a single task 

3. Handlers are dynamically spun up, and they manage dependent Pods' CPU needs.

*Multiple facilities for content processing  *

1. Support for firewall-protected facilities using their own Kubernetes cluster to process content 

2. Customers who desire segregated resources within the same cluster can use namespace segregation. 

*Concurrent processing *

1. Able to perform tasks in parallel without any waiting dependencies


