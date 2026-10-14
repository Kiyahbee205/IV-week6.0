# IV-week6.0
# HarborTech Internal Note: Compute Model Recommendation

**Ticket:** TKT-2026-0006  
**Subject:** Evaluating Virtual Machines, Containers, and Serverless Computing  
**Prepared by:** Junior Cloud Operations Intern

## 1. Current Server-Based Condition

The client currently runs two workloads on one EC2 instance. The first is a daily reporting task that runs once each morning and stops when finished. The second is a small REST API that receives intermittent requests throughout the day. The instance remains lightly utilized for long periods, but HarborTech and the client still carry the operational responsibilities associated with the guest operating system, application runtime, updates, and troubleshooting.

The goal is to determine whether each workload would benefit from a virtual machine, container, or serverless solution while reducing unnecessary infrastructure management without sacrificing reliability or security.

## 2. Evidence and Compute Model Recommendations

**Workload A: Daily Reporting Task**

The available evidence shows that the report runs once each morning, completes quickly, keeps no local state between runs, and leaves the EC2 instance idle for much of the day.

**Recommendation:** Evaluate AWS Lambda with a scheduled trigger. This workload appears well suited to serverless execution because it runs periodically and does not require a continuously running server. A dedicated VM may introduce unnecessary operational effort, while containerization has not been shown to provide a clear advantage for this short-running task.

**Workload B: Intermittent REST API**

The API receives requests throughout the day, but traffic is intermittent and the shared EC2 instance remains lightly utilized for extended periods. Exact request volumes, concurrency, and response-time requirements have not yet been provided.

**Recommendation:** Evaluate Amazon API Gateway with AWS Lambda. API Gateway could receive HTTP requests and route them to Lambda, which would execute the application logic and return responses. This design could reduce server-management responsibilities while supporting variable request demand. A container or VM should remain an alternative if application dependencies, persistent processes, or host-level requirements make serverless unsuitable.

## 3. Operational Responsibility Tradeoff

With the proposed serverless design, AWS would manage the underlying execution infrastructure for Lambda and the managed infrastructure supporting API Gateway. HarborTech would have less responsibility for provisioning servers and maintaining guest operating systems for these workloads.

HarborTech would still be responsible for application code, configuration, IAM permissions, authentication and authorization, data protection, testing, logging, monitoring, application-level troubleshooting, and cost management. If the workloads require access to private network resources, HarborTech would also need to ensure that the appropriate VPC connectivity and security controls are configured.

Containers could improve application portability and dependency consistency, but they do not automatically eliminate infrastructure management. A VM would retain the greatest level of guest operating system control while also requiring more direct operating system maintenance.

## 4. Validation Evidence to Collect

Before approving a migration, I would collect and review the following evidence:

- **Reporting task:** Actual execution duration, schedule, memory requirements, file and dependency requirements, data access needs, and compatibility with the Lambda runtime and execution limits.
- **REST API:** Request volume, peak concurrency, response-time requirements, payload sizes, authentication requirements, database connections, and expected availability.
- **Current EC2 environment:** CPU and memory utilization, operating system dependencies, current costs, application logs, and other services that depend on the shared instance.
- **IAM and security:** Required execution-role permissions, resource access, API security configuration, and any sensitive data handled by either workload.
- **Networking:** Whether either workload needs private VPC connectivity and whether routing, security groups, and other network controls support the proposed design.
- **Monitoring and testing:** Lambda execution logs, error rates, duration metrics, report accuracy, API functional tests, load tests, and failure-recovery behavior.
- **Cost and service limits:** Estimated charges, expected invocation volume, execution duration, concurrency requirements, API Gateway usage, and applicable service quotas.
- **Migration readiness:** Deployment requirements, integration testing, rollback procedures, and the effect of moving each workload away from the existing EC2 instance.

The ticket provides enough evidence to justify evaluating serverless options, but the additional technical and operational requirements must be confirmed before a final architecture is selected.

## 5. Escalation and Implementation Decisions

I would escalate the following decisions to the authorized cloud architect or project owner before implementation:

1. Approval to migrate either workload from the shared EC2 instance.
2. Confirmation of the final compute architecture and any supporting AWS services.
3. Approval of IAM execution roles, API access controls, and any required network changes.
4. Review of performance, reliability, service-limit, and cost findings.
5. Approval of the deployment schedule, testing plan, rollback procedures, and production change.

The key assumptions to validate are that the reporting task can run within Lambda's supported environment and that the API does not depend on persistent processes or host-level access that Lambda cannot provide. If either assumption fails, a VM or container-based design should be reconsidered based on the application's actual requirements.

As a junior cloud operations intern, I would document the evidence, complete authorized testing, and submit the recommendation for review. I would not make unapproved infrastructure, permission, or production changes.

## 6. Final Recommendation

HarborTech should evaluate AWS Lambda for the daily reporting task and Amazon API Gateway with Lambda for the intermittent REST API. These approaches could reduce unnecessary server management and better match the workloads' observed behavior. Virtual machines and containers should remain alternatives if testing identifies operating system, dependency, persistence, or troubleshooting requirements that make serverless unsuitable.

The recommendation is preliminary. The final decision should be based on verified workload requirements, security and cost reviews, successful testing, and approval from the authorized decision-maker. No production migration should proceed until the required validation and approvals are complete.
