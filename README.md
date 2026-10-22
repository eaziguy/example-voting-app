# Voting App — DevOps Capstone Project

A distributed voting application deployed using Docker, Kubernetes, and Helm. This project demonstrates containerization, CI/CD automation, vulnerability scanning, Kubernetes deployment, and release management.

## Project Overview

The application allows users to vote between two options and displays the results in real time.

The application consists of five services:

* **Vote:** Python web application that accepts votes.
* **Redis:** Stores votes temporarily for processing.
* **Worker:** Processes votes and stores them in PostgreSQL.
* **PostgreSQL:** Stores processed voting data.
* **Result:** Node.js web application that displays voting results.

## Architecture

```text
                 Voters
                    |
                    v
              Vote Service
               Port 8080
                    |
                    v
                  Redis
                    |
                    v
             Worker Service
                    |
                    v
               PostgreSQL
                    ^
                    |
              Result Service
               Port 8081
                    |
                    v
              View Results
```

## Technology Stack

* Docker and Docker Compose
* Kubernetes
* Kind (local Kubernetes cluster)
* Helm
* Terraform
* GitHub Actions
* Trivy
* Python
* Node.js
* .NET
* Redis
* PostgreSQL

## Running Locally with Docker Compose

### Prerequisites

Install Docker Desktop, which includes Docker Compose.

### Start the application

From the repository root, run:

```bash
docker compose up --build
```

Open the following URLs:

* Voting application: http://localhost:8080
* Results application: http://localhost:8081

Stop the application with:

```bash
docker compose down
```

## Kubernetes Deployment with Helm

The project includes a Helm chart in `voting-app/`.

### Validate the chart

```bash
helm lint ./voting-app
```

### Render Kubernetes manifests

```bash
helm template voting-app ./voting-app
```

### Deploy the application

```bash
helm upgrade --install voting-helm ./voting-app --namespace voting-helm --create-namespace
```

### Access the application

In separate terminals, run:

```bash
kubectl port-forward -n voting-helm svc/vote 8180:8080
```

```bash
kubectl port-forward -n voting-helm svc/result 8181:8081
```

Open:

* Voting application: http://localhost:8180
* Results application: http://localhost:8181

### Check deployment status

```bash
kubectl get pods -n voting-helm
```

```bash
helm status voting-helm -n voting-helm
```

## Security Improvements

The Helm deployment templates include security settings intended to reduce container privileges.

These include:

* Running Vote, Result, and Worker as a non-root user.
* Disabling privilege escalation for the application containers.
* Dropping Linux capabilities.
* Using a read-only root filesystem for Vote, Result, and Worker where configured.

**Note:** The PostgreSQL container requires special consideration because its initialization process needs to manage directory permissions. Database security settings must be tested carefully. The current chart also uses a demonstration database password that should be replaced with a Kubernetes Secret or an appropriate secret-management solution before production use.

## CI/CD and Vulnerability Scanning

GitHub Actions workflows are included in the repository to automate build and security-related tasks.

The project uses Trivy to scan for vulnerabilities, misconfigurations, and exposed secrets.

Workflow status and execution logs are available in the repository's GitHub Actions page.

A successful workflow run confirms that the configured workflow completed successfully; vulnerability findings must still be reviewed separately.

## Helm Release Management and Rollback

Helm maintains revision history for releases, making it possible to return to a previous release configuration.

View release history:

```bash
helm history voting-helm -n voting-helm
```

View release status:

```bash
helm status voting-helm -n voting-helm
```

Roll back to a previous revision:

```bash
helm rollback voting-helm 1 -n voting-helm
```

Replace `1` with the desired revision number after reviewing the history.

**Caution:** A rollback changes the deployed release. Confirm the target revision before running the command.

## Terraform and Cloud Deployment

Terraform is part of the project's infrastructure-as-code toolchain.

The AWS Kubernetes cluster requirement remains a separate outstanding task. The application has been deployed to a local Kind cluster; this does not demonstrate that the AWS infrastructure can be created and destroyed through Terraform.

Cloud deployment should be completed only after AWS credentials, permissions, costs, and infrastructure configuration have been reviewed.

## Current Project Status

Completed or demonstrated:

* Local Kubernetes cluster running the application.
* Helm chart validation.
* Helm-based deployment and release history.
* GitHub Actions workflows.
* Trivy scanning workflow.
* Security-related changes to Helm deployment templates.

Remaining work includes:

* Verifying that all security settings are compatible with each container.
* Reviewing Trivy findings and resolving outstanding high-severity issues.
* Completing and demonstrating the Terraform-managed AWS infrastructure requirement.
* Confirming the final documentation and architecture diagram.

## Repository Structure

```text
.
├── .github/
│   └── workflows/
├── voting-app/
│   ├── Chart.yaml
│   ├── values.yaml
│   └── templates/
├── vote/
├── result/
├── worker/
├── docker-compose.yml
└── README.md
```

The exact repository contents may vary as the project evolves.

## Learning Objectives

This project provides practical experience with:

* Containerizing a multi-service application.
* Deploying applications to Kubernetes.
* Managing deployments with Helm.
* Automating workflows with GitHub Actions.
* Scanning code and configuration with Trivy.
* Applying container security settings.
* Managing release revisions and rollbacks.
* Understanding infrastructure as code with Terraform.
