### Project Discussion.

> **Explain your application's complete journey from a developer pushing code to a successful production deployment. Describe the role of Harness, Artifactory, Docker, Argo CD, Kubernetes, and Terraform in your architecture.**
> 
> **Follow-up probes -   
How do you ensure the exact image tested is the image deployed?  
What happens if the image push succeeds but the deployment fails?  
How do you implement approvals, rollback, and environment promotion?  
How would you onboard a new microservice repository without duplicating the entire pipeline?**

The thumb rule - Context and requirement (what problem to solve) - Architecture and flow - The implementation - Failure handling and trade offs - Outcome.


Describing the project and experience - dont give it in steps without explaining the architecture, ownership boundaries and engineering decision.

Your role - SDE 2 Spring boot microservice development, manage the Kafka access provisioning through terraform after data governance approvals, working hands-on in the configuration of the Harness CI/CD, code-quality checks, and deployment integrations and then the GitOps deployment flow through Argo CD and Kubernetes and maintaining the application in prod performing the observability experience with Dynatrace, dashboards, and incident alerting. 


## Todo - Enhancing the Confluent platform and automating the process of the ACL to the Kafka topic and the RBAC to control the consumer group.

In the project dont say I did this and implementation. The first part make sure to explain the architecture.

SonarQube analyzes code quality and duplication and can enforce quality gates. JaCoCo measures Java test coverage and supplies coverage data to SonarQube. Secret scanning is a separate control.

Kafka ACLs can control topic and consumer-group operations; Confluent RBAC can grant resource-role permissions depending on the cluster and security configuration.


<br><br>
Sample Answer - My current role combines Java backend development with deployment automation and platform-related infrastructure work. I'll explain the lifecycle using one of our Kafka-consuming Spring Boot microservices as an example.

At a high level, the flow is: application development and testing → access and infrastructure prerequisites → Harness CI/CD → container registry → GitOps configuration → Argo CD → Kubernetes deployment → production monitoring.


**Application development and access prerequisites**.

I start by developing the Spring Boot microservice based on the business requirement. For example, the service consumes messages from a Kafka topic and forwards the required data to a downstream analytics team.

I implement the application, add the necessary tests and validate the service. In parallel, I arrange the required Kafka permissions. Our access process involves data-governance approval through Collibra and the relevant administrative approval.

One of my infrastructure contributions is automating the approved access-provisioning workflow using Terraform. The automation adds the approved identity to the appropriate Active Directory group for the required access. Kafka topic and consumer-group permissions are governed by the corresponding authorization configuration. Some RBAC-related automation remains outside the current automated scope.


For deployment, the required namespace and access prerequisites are also arranged through the organization's existing request and approval process.

**CI/CD pipeline using Harness**.

Once the application and its prerequisites are ready, I configure the CI/CD workflow in Harness.

The CI stage builds the Java application, executes tests, checks code quality and coverage, and performs secret scanning. We use SonarQube for code-quality analysis and quality gates, with JaCoCo providing Java test-coverage information.

After the checks pass, the pipeline builds and publishes the Docker image to our container registry. The image is associated with a version or identifier so that the deployment configuration can reference the intended application build.

**GitOps deployment through Argo CD**.

For deployment, we use an existing Argo CD App of Apps structure. I configure the application-specific deployment configuration and YAML manifests in the relevant Git repository, following our namespace and repository conventions.

The deployment configuration specifies the container image and Kubernetes workload settings, together with the required configuration and secret references. Argo CD monitors the relevant Git configuration and reconciles the desired state with the Kubernetes cluster.

I have configured the application-specific Harness pipeline and deployment configuration. The underlying App of Apps integration was already established by the platform, so I work within that existing architecture rather than claiming to have built the entire integration from scratch.


**Production validation and observability**.

Once the deployment is synchronized, I verify the application health and the state of the Kubernetes workloads. We use Dynatrace dashboards to monitor application requests, successful responses, failures and other relevant operational signals.

Production alerting is configured to notify the responsible support group when defined conditions are met. This helps us investigate incidents and determine whether the issue is related to the application, its dependencies or the deployment environment.

Overall, my contribution spans the application itself, Kafka access automation using Terraform, Harness pipeline configuration, GitOps-based deployment configuration and production observability. This gives me experience across both the software development lifecycle and the operational requirements of running services reliably.
### You said you created the Harness pipeline. How would you scale this to 50 microservices?

In my current experience, I have configured a Harness pipeline for an application. At an organizational level, I would avoid duplicating the same pipeline logic independently across every repository.

I would create reusable Harness pipeline templates for common stages such as Java builds, unit tests, quality gates, secret scanning, image publication, and deployment integration.

Each application repository would provide its specific configuration, such as the repository, build parameters, image name, and deployment environment. The shared templates would enforce common engineering and security standards while supporting approved application-specific variations.

I would version the templates, test changes before rolling them out, and provide a documented onboarding process for new services.

This would improve consistency, reduce duplication, and make platform-wide improvements easier to maintain.

### What is the difference between Argo CD sync status and application health?

Sync status indicates whether the live Kubernetes resources match the desired configuration in Git. Application health indicates whether the resources are operating in a healthy state according to Argo CD's health assessment.

For example, Argo CD may successfully synchronize a Deployment, but the new Pods may fail because of an incorrect configuration, an image-pull problem, or an application startup error.

Therefore, I would verify both synchronization and health, then inspect Deployment rollout status, Pod events, application logs, and readiness probes before considering the deployment successful.

### How would you troubleshoot a production issue?

I would start by establishing the impact, such as elevated errors, increased latency, unavailable Pods, or growing Kafka consumer lag.

I would correlate the time of the incident with recent deployments and inspect Dynatrace dashboards, application logs, Kubernetes events, resource utilization, and relevant Kafka metrics.

For example, if Pods are restarting, I would inspect their previous logs and termination reasons. If the Pods are healthy but consumer lag is increasing, I would investigate message-processing throughput, downstream latency, consumer health, and Kafka connectivity.

After identifying the likely cause, I would mitigate the impact, validate recovery, and follow up with a preventive improvement such as a code fix, resource adjustment, improved alert, or additional test.



### How would you design ephemeral environments?

I would build this as a self-service workflow in which an engineer requests an isolated environment for a feature branch or test run.

The workflow would validate the request, provision required infrastructure through reusable Terraform modules where appropriate, and deploy the application and dependencies into an isolated Kubernetes namespace or environment.

Harness would orchestrate the workflow, while GitOps and Argo CD could manage the desired Kubernetes configuration. Automated integration tests would execute after the environment became healthy.

Finally, the workflow would destroy the temporary resources after completion or expiry, with safeguards for failed cleanup, access isolation, secrets, and cloud-cost controls.

I would distinguish infrastructure provisioned through Terraform from application resources managed through Kubernetes and GitOps, ensuring that ownership and cleanup responsibilities are clear.
### How would you make the platform secure?


I would apply security controls throughout the delivery lifecycle rather than treating security as a final deployment check.

In CI, I would enforce unit tests, code-quality gates, secret scanning, dependency checks, and container image scanning. I would restrict production deployment permissions and require the relevant approvals.

For runtime security, I would use least-privilege service identities, restrict network access, and integrate with the organization's approved secret-management solution. Secrets should be referenced securely at runtime rather than committed as plaintext in Git.

I would also maintain auditable records of access approvals, pipeline executions, artifact versions, and deployments, and configure monitoring to detect operational or security issues.


### Topic.

CI versus CD.

CI builds and tests the application, performs quality and security checks, and publishes the artifact. CD coordinates its release and deployment. Your organization may implement both through Harness, but Argo CD performs the Kubernetes reconciliation in your described GitOps setup.

The important platform-engineering principle is that access should be approved, least-privileged, auditable, and repeatable rather than granted through uncontrolled manual changes.

### Kafka access provisioning from approved Collibra requests using Terraform.

The end-to-end flow.

Collibra — approved access request - User identity, AD group, Kafka topic, consumer group, approval ID.  

