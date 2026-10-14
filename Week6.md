# Week 6: Containers and Serverless Computing

## HarborTech Ticket Summary

**Ticket ID:** TKT-2026-0006  
**Topic:** Containers and Serverless Computing

This ticket focuses on two workloads currently sharing one EC2 instance. The first workload is a reporting task that runs once each morning, completes quickly, and then stops. The second workload is a small REST API that receives intermittent requests throughout the day.

The current EC2 instance remains lightly utilized for long periods, but the client still has to manage the guest operating system, application runtime, updates, and troubleshooting.

The goal is to determine whether virtual machines, containers, or serverless computing would be the best fit for each workload. Based on the available evidence, AWS Lambda is the recommended option to evaluate for the reporting task, while Amazon API Gateway with Lambda is recommended for the REST API. Both recommendations require additional testing and approval before implementation.

## Client Impact

Using an inefficient compute model can increase costs and create unnecessary work for the IT team. For example, keeping an EC2 instance running for a reporting task that only runs once a day can leave compute resources underused.

Reliability is another concern because both workloads currently share one instance. An instance failure or maintenance activity could affect both workloads. This is a potential risk, not evidence that an outage has already occurred.

Scalability also matters because the API may receive more requests during certain periods. A serverless design could help accommodate changing demand, but performance, concurrency, and service limits must be tested.

Moving to managed serverless services could reduce the time spent maintaining servers. However, HarborTech would still need to manage application code, security, permissions, testing, monitoring, and troubleshooting.

## AWS Services Involved

- **Amazon EC2:** Provides virtual machines and currently hosts both workloads.
- **Containers:** Package application code and dependencies together to help provide a consistent runtime environment.
- **Serverless Computing:** Allows applications to run without the customer directly managing the underlying execution servers.
- **AWS Lambda:** Runs code in response to events, schedules, or requests without requiring HarborTech to manage a guest operating system.
- **Amazon API Gateway:** Receives and manages API requests and can route them to Lambda functions.
- **AWS Step Functions:** Coordinates multiple steps in an application workflow and supports branching, retries, and error handling. It may be useful if the reporting process becomes more complex, but the current ticket does not establish that it is needed.
- **IAM Execution Roles:** Grant Lambda functions permission to access approved AWS resources.
- **Event-Driven Computing:** Starts processing when an event occurs, such as a scheduled report or incoming API request.
- **VPC-Connected Lambda:** Allows a Lambda function to connect to resources in an Amazon VPC when private network access is required.
- **Amazon CloudWatch:** Collects logs and metrics and supports monitoring and alarms for identifying errors and performance problems.

These services are being considered for the proposed design. The available evidence does not confirm that a new architecture has been deployed.

## Virtualization Connection

Virtual machines, containers, and serverless computing provide different levels of abstraction.

A virtual machine such as an EC2 instance runs its own guest operating system. HarborTech has greater control over the operating system, but it also has more responsibility for operating system updates, configuration, and troubleshooting.

Containers package application code and dependencies while sharing the host operating system kernel. They can make applications easier to move between environments, but the container platform and underlying infrastructure still require management unless a managed service handles those responsibilities.

Serverless computing provides a higher level of abstraction by allowing AWS to manage more of the underlying execution infrastructure. With Lambda, HarborTech can focus on application code and configuration instead of maintaining a guest operating system for each function.

The main lesson is that each compute model offers a different balance of control and operational responsibility. More abstraction can reduce infrastructure management, but it does not eliminate application maintenance or security responsibilities.

## Evidence Reviewed

The following evidence was provided in the HarborTech ticket:

- **Reporting task frequency:** The report runs once each morning.
- **Reporting task duration:** The report completes quickly and then stops.
- **Local state:** The reporting task does not keep local state between runs.
- **API request pattern:** The REST API receives intermittent requests throughout the day.
- **Server utilization:** The shared EC2 instance remains lightly utilized for long periods.
- **Current operational responsibilities:** The client still manages responsibilities associated with the guest operating system and application runtime.

The following information has not yet been confirmed and must be collected before implementation:

- **Runtime and dependencies:** Whether either workload depends on specific operating system packages, local files, or persistent processes.
- **IAM permissions:** Which AWS resources each proposed Lambda function must access and what permissions are required.
- **API behavior:** Actual request volume, peak concurrency, payload sizes, and response-time requirements.
- **VPC connectivity:** Whether either workload needs access to private resources.
- **Lambda execution evidence:** Logs, errors, duration, memory utilization, and successful test results from a proposed implementation.
- **Workflow requirements:** Whether the reporting task includes multiple steps that require orchestration or special failure handling.
- **Monitoring and costs:** Current EC2 costs, expected serverless usage, CloudWatch evidence, and estimated service charges.

No Lambda execution logs, migration test results, or completed permission changes were provided in the ticket evidence. These should not be reported as completed work.

## Operational Analysis

**Workload A: Daily Reporting Task**

The reporting task is a strong candidate for serverless computing because it runs once each morning, finishes quickly, and does not retain local state between executions.

AWS Lambda could run the reporting code on a schedule without requiring a dedicated EC2 instance to remain running for this task. Before selecting Lambda, HarborTech should confirm the execution duration, runtime compatibility, memory requirements, and data access needs.

**Workload B: Intermittent REST API**

Amazon API Gateway with AWS Lambda is the preferred architecture to evaluate for the API. API Gateway would receive HTTP requests and route them to the appropriate Lambda function. Lambda would execute the application logic and return a response.

This design could reduce the need to manage a continuously running application server. However, HarborTech must test API authentication, error handling, latency, concurrency, and any database or service integrations.

**Container and VM alternatives**

Containers remain an option if application dependencies need to be packaged together or if the application requires a container-based deployment environment. A VM may be more appropriate if the application requires direct guest operating system control, host-level troubleshooting, or a persistent process that does not fit Lambda's execution model.

AWS Step Functions should only be introduced if evidence shows that the reporting task or API requires coordinated multi-step processing.

## Recommendation

HarborTech should evaluate the following compute models:

1. **Daily reporting task:** AWS Lambda with an approved scheduled trigger.
2. **Intermittent REST API:** Amazon API Gateway integrated with AWS Lambda.
3. **Container alternative:** Consider containers if dependency packaging or deployment requirements justify the additional platform decisions.
4. **VM alternative:** Retain EC2 if testing identifies persistent processes, custom operating system requirements, or host-level access needs.
5. **Monitoring:** Use Amazon CloudWatch logs and metrics to evaluate errors, execution duration, and performance.
6. **Security:** Review IAM execution roles and grant only the permissions needed for each workload.
7. **Cost evaluation:** Compare current EC2 costs with expected Lambda, API Gateway, monitoring, and data-transfer charges.

AWS would manage more of the underlying execution infrastructure in the proposed serverless design. HarborTech would remain responsible for application code, security configuration, permissions, testing, monitoring, integrations, and cost management.

The recommendation is preliminary. Successful testing and approval are required before either workload is migrated.

## Escalation Notes

The following items require review by the authorized cloud architect or project owner:

- **Architecture approval:** Confirm the final compute model and any supporting AWS services.
- **Application compatibility:** Verify that the reporting task and API can operate within the proposed Lambda environment.
- **IAM permissions:** Obtain approval for required execution roles and resource access.
- **Networking:** Confirm whether private VPC connectivity or network changes are necessary.
- **Security:** Review API authentication, authorization, and data protection requirements.
- **Cost and performance:** Validate cost estimates, concurrency needs, service quotas, latency, and availability requirements.
- **Production migration:** Obtain approval for the deployment schedule, testing plan, rollback procedures, and any changes affecting production.

As a junior cloud operations intern, I would document the evidence, perform authorized testing, and submit my findings for review. I would not make unapproved infrastructure or permission changes.

No production migration or architecture approval has been confirmed by the evidence currently available.

## Lessons Learned

Week 6 taught me that the best compute model depends on how a workload operates and what resources it requires. A short daily reporting task may not need a continuously running EC2 instance, while an intermittent API may benefit from API Gateway and Lambda.

I also learned that containers and serverless computing are not the same. Containers package applications and dependencies, while serverless computing reduces the need to manage the underlying execution infrastructure.

AWS manages some infrastructure responsibilities, but HarborTech still has to maintain application code, protect data, configure permissions, test functionality, and monitor performance.

Before recommending a migration, I should collect evidence about workload behavior, application dependencies, networking, security, and costs. I should also document unknowns and escalate architecture and production decisions for approval.

## Professional Vocabulary

- **Container:** A package containing an application and its dependencies that runs in a consistent environment while sharing the host operating system kernel.
- **Serverless Computing:** A computing model where AWS manages the underlying execution infrastructure so the customer can focus on application code and configuration.
- **AWS Lambda:** An AWS service that executes code in response to events, schedules, or requests.
- **API Gateway:** An AWS service that receives and manages API requests and routes them to backend services.
- **Execution Role:** An IAM role that grants a service or function permission to perform approved actions on AWS resources.
- **Event Trigger:** An event or schedule that invokes a task or function.
- **Step Functions:** An AWS service that coordinates multiple steps in an application workflow.
- **VPC-Connected Lambda:** A Lambda function configured to access resources through an Amazon VPC when private network connectivity is required.
- **Runtime:** The software environment used to execute application code.
- **Abstraction:** Hiding lower-level implementation details so users can work with a simpler service or interface.
- **Virtual Machine (VM):** A software-based computer that runs its own guest operating system on virtualized hardware.
- **Event-Driven Computing:** A design where processing begins in response to an event instead of requiring a continuously running application.
- **CloudWatch:** An AWS monitoring service used to collect metrics and logs and configure alarms.
- **Scalability:** The ability of a system to handle increases or changes in workload demand.
- **Least Privilege:** A security principle that grants only the permissions needed to perform a specific task.
- **Operational Responsibility:** The work required to maintain, secure, monitor, troubleshoot, and operate a system.
