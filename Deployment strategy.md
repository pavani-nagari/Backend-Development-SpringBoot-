# From Pull Request to Deployment: Azure DevOps + AKS

This document explains the typical path from a code change in Azure Repos to an application update in an Azure Kubernetes Service (AKS) Dev environment.

> **Note:** This describes the workflow based on the pipeline steps observed so far. The exact deployment mechanism, container registry, and whether deployment runs in the same or a separate pipeline depend on the team's configuration.

## 1. End-to-end overview

```text
Developer changes code
        |
        v
Push feature branch to Azure Repos
        |
        v
Create Pull Request (PR)
        |
        v
PR review + configured validation checks
        |
        v
PR approved and merged
        |
        v
Create/select a Git tag for a specific commit
        |
        v
Azure Pipeline starts for that tag
        |
        v
Initialize build environment / helper containers
        |
        v
Checkout repository + set pipeline variables
        |
        v
Install dependencies -> Test -> Build
        |
        v
Publish unit-test report
        |
        v
Package and upload Helm chart to artifact repository
        |
        v
[Application image/package is published as configured]
        |
        v
Deployment pipeline/stage uses the selected version and Helm chart
        |
        v
AKS updates the workload in the Dev namespace
        |
        v
Pods become ready -> verify application in Dev
```

## 2. Step-by-step process

### Step 1 — Develop and push code

A developer makes changes locally, commits them to a feature branch, and pushes the branch to Azure Repos.

Git records the commit and its unique commit hash. At this point, the source code is in the repository; it is not necessarily deployed to Dev.

### Step 2 — Create a Pull Request

The developer opens a PR to merge the feature branch into the target branch.

Depending on repository policies and pipeline configuration, the PR process can include:

- Peer review and approval.
- Build validation.
- Unit tests and other quality checks.
- Branch policy checks.

When required approvals and checks pass, the PR is completed and the changes are merged.

### Step 3 — Create or select a Git tag

A Git tag identifies a particular commit, often to mark a build or release version. For example:

```text
Commit: a1b2c3d
Tag:    workorders-1.4.0
```

A tag does not automatically trigger a build or deployment unless the pipeline is configured to respond to tags, or someone manually starts a run using that tag.

### Step 4 — Azure Pipeline initializes the build

The pipeline allocates an agent or execution environment and runs its configured steps. The observed steps include:

1. **Initialize containers** — prepare any helper containers or supporting services needed for the job.
2. **Checkout repository** — retrieve the source code for the selected commit/tag.
3. **Set pipeline variables** — configure values used by build and packaging tasks, such as versions and repository settings.
4. **Initialize pipeline start time for metrics** — record a start time to measure pipeline duration or report metrics.

The exact purpose of “initialize containers” depends on the pipeline implementation; these containers are not necessarily the application's AKS containers.

### Step 5 — Install, test, and build

The pipeline installs or restores dependencies, executes configured checks and tests, and builds the application.

For a Java application, this might involve Maven or Gradle. The actual commands depend on the repository and pipeline scripts.

The result may be a JAR/WAR, a Docker image, or another package. Do not assume a Docker image is built unless the pipeline logs or YAML show a Docker build/push task.

### Step 6 — Publish the unit-test report

The pipeline collects test results and publishes them to Azure DevOps so the run shows test counts and failures. For Java, test reports may be generated in JUnit/Surefire XML format.

Publishing a report makes results visible; it does not guarantee that every test passed. The build's success/failure depends on the configured rules.

### Step 7 — Package and upload the Helm chart

A **Helm chart** packages Kubernetes deployment templates and configuration values. It describes resources Kubernetes should create or update, such as Deployments, Services, and configuration references.

The pipeline packages the chart and uploads it to a configured artifact repository, which may be JFrog Artifactory.

A Helm chart is **not** the running application and is not the same as a Docker image:

- **Docker image:** application code and its runtime/dependencies.
- **Helm chart:** deployment templates and configuration for Kubernetes.
- **Artifactory:** a repository that can store versioned build artifacts, such as Helm charts and, if configured, container images.

Uploading the Helm chart alone does not deploy it to AKS.

### Step 8 — Deployment stage or pipeline

After the build/package pipeline, a deployment stage or a separate release pipeline must use the appropriate application version and deployment configuration.

In a common AKS workflow, it may:

1. Retrieve the Helm chart from the artifact repository.
2. Identify the intended application image and version.
3. Connect to the target AKS cluster and Dev namespace using configured service connections/permissions.
4. Run Helm (for example, an upgrade/install operation) or another deployment command.
5. Apply the Kubernetes configuration.

The exact steps must be confirmed in the deployment pipeline logs or YAML. The build pipeline may only publish artifacts and leave deployment to another pipeline.

### Step 9 — Kubernetes updates the application in AKS

AKS is Azure's managed Kubernetes service. Kubernetes compares the desired workload configuration with the current state.

Depending on the Deployment configuration and rollout strategy, Kubernetes may create new pods with the updated version and gradually replace old pods. It monitors readiness according to the configured probes and rollout settings.

Useful terms:

- **AKS cluster:** the managed Kubernetes environment.
- **Node:** a worker machine where pods can run.
- **Pod:** the Kubernetes unit that contains one or more containers.
- **Container:** the running application process packaged from an image.
- **Deployment:** a Kubernetes resource that manages replicas and application rollouts.
- **Service:** a stable network endpoint for reaching application pods.
- **Namespace:** a logical grouping of Kubernetes resources, often used to separate environments or teams.
- **Helm:** a tool for installing and updating Kubernetes applications using charts.

If the rollout succeeds and the application is reachable, the updated behavior becomes available in Dev.

### Step 10 — Verify the deployment

After deployment, check the pipeline/release result and verify the application in Dev. Depending on access and tooling, useful checks include:

- Confirm the deployed image tag or application version.
- Check rollout status and pod readiness.
- Review application/container logs if the service does not behave as expected.
- Run a smoke test or verify the changed API/UI behavior.

A successful build is not the same as a successful deployment. Confirm both stages when troubleshooting.

### Step 11 — Stop containers / cleanup

The observed pipeline ends with **stop containers**. This likely cleans up helper or supporting containers used by the build job. It does not necessarily stop the application containers running in AKS; those are managed separately by Kubernetes.

## 3. Quick comparison of the main components

| Component | Main responsibility |
|---|---|
| Azure Repos | Stores source code, commits, branches, and tags |
| Pull Request | Reviews and merges changes; can enforce validation policies |
| Azure Pipelines | Orchestrates build, test, packaging, and deployment jobs |
| Build agent | Executes the pipeline steps |
| Dockerfile | Instructions for building a Docker image, if Docker is used |
| Docker image | Packaged application and runtime/dependencies |
| Helm chart | Kubernetes deployment templates and configuration |
| Artifactory | Stores versioned artifacts, depending on repository setup |
| AKS | Hosts and manages the Kubernetes workloads |
| Pod/container | Runs the application in the cluster |

## 4. How to confirm the exact workflow in your project

Inspect the pipeline run and its YAML/templates:

1. **Build logs:** look for Maven/Gradle tasks, `docker build`, `docker push`, chart packaging, and artifact upload.
2. **Helm upload task:** identify the chart name/version and repository destination.
3. **Deployment stage or separate pipeline:** look for a stage such as `Deploy Dev`, or commands/tasks such as `helm upgrade`, `KubernetesManifest`, `Kubernetes`, `kubectl apply`, or Helm deployment tasks.
4. **Target configuration:** identify the AKS cluster and namespace from approved configuration; do not publish credentials or secrets.
5. **Image reference:** determine which registry and image tag the chart points to.

## 5. Key takeaways

- A PR is for review, validation, and merging.
- A Git tag identifies a specific commit; it triggers a pipeline only if configured or selected manually.
- The build pipeline installs dependencies, tests, builds, and publishes artifacts.
- A Helm chart describes how Kubernetes should deploy the application.
- A deployment stage/pipeline applies the chart and selected application version to AKS.
- Kubernetes manages pods and rollout behavior.
- The exact image registry, build output, and deployment trigger must be verified in your team's pipeline configuration.

## Suggested README diagram

```text
Azure Repos (PR + merge + tag)
             |
             v
Azure Pipeline (checkout, variables, install/test/build)
             |
             +----> Unit-test report
             |
             +----> Application package/image (as configured)
             |
             +----> Helm chart -> Artifactory
                                      |
                                      v
                           Deployment stage/pipeline
                                      |
                                      v
                                  Azure AKS
                                      |
                                      v
                              Dev pods / application
```
