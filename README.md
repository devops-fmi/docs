# DevOps Course Project: E-Library Infrastructure and Application

This project is a university course assignment focused on provisioning AWS infrastructure for an arbitrary application using Amazon EKS (Elastic Kubernetes Service). The project consists of (as of now) two repositories:

- [**bun-server-demo**](https://github.com/devops-fmi/bun-server-demo): A TypeScript-based e-library server application built with Bun
- [**terraform-iac**](https://github.com/devops-fmi/terraform-iac): Infrastructure as Code using Terraform to provision AWS resources

## Project Overview

The goal is to create a complete DevOps pipeline that demonstrates modern cloud-native application deployment practices, including infrastructure provisioning, containerization, CI/CD workflows, and GitOps principles.

## Technologies Used

### Application Layer
- **Bun**: JavaScript runtime and toolkit for fast development
- **TypeScript**: Strongly typed programming language
- **Elysia**: Modern web framework for building APIs
- **OpenAPI**: API specification and documentation

### Infrastructure Layer
- **Terraform**: Infrastructure as Code tool
- **Amazon EKS**: Managed Kubernetes service
- **Amazon ECR**: Container registry
- **Amazon VPC**: Virtual private cloud networking
- **ArgoCD**: GitOps continuous delivery tool
- **Helm**: Package manager for Kubernetes

### CI/CD and Quality Assurance
- **GitHub Actions**: CI/CD platform
- **SonarLint**: Code quality and security analysis
- **Snyk**: Vulnerability scanning
- **Docker**: Containerization platform

## Application Architecture

The e-library server exposes a REST API with OpenAPI specification for managing library resources. The domain model consists of three main entities:

### Domain Entities

- **User**: Represents library users with properties like name, email, role (admin/user), and creation timestamp
- **Book**: Represents books in the catalog with title, author, ISBN, genre, publication year, quantity, and creation timestamp
- **Library**: Represents physical library branches with name, location, manager (user reference), total books count, and creation timestamp

The application follows a layered architecture with:
- **Repository Layer**: Data access abstraction (currently in-memory implementation)
- **Service Layer**: Business logic
- **Handler Layer**: HTTP request/response handling

### API Endpoints

The API provides the following top-level endpoints:

- `/health`: Health check endpoint
- `/openapi`: OpenAPI specification web UI (powered by `Scalar`)
- `/openapi/json`: OpenAPI specification in JSON format
- `/users`: User management endpoints
- `/books`: Book catalog management endpoints
- `/libraries`: Library branch management endpoints

## Infrastructure Setup

The infrastructure is provisioned using Terraform and includes:

### Networking
- **VPC**: Isolated virtual network with public and private subnets across multiple availability zones
- **Subnets**: Public subnets for load balancers, private subnets for EKS nodes
> The deployed server is publicly accessable at [**http://library.app.fmi.genadikolev.eu**](http://library.app.fmi.genadikolev.eu)

### Kubernetes
- **EKS Cluster**: Managed Kubernetes control plane with configurable node groups
- **ArgoCD**: Installed via Helm for GitOps deployments

### Container Registry
- **ECR Repository**: Private registry for storing Docker images

### State Management
- **S3 Backend**: Remote Terraform state storage with locking for team collaboration

### CI/CD Workflows

#### Application Repository (bun-server-demo)
- **Pull Request Validation**: Runs tests, linting, and security scans
- **Container Build**: Builds Docker image and pushes to ECR on merge
- **Quality Gates**: SonarLint for code quality, Snyk for dependency vulnerabilities

#### Infrastructure Repository (terraform-iac)
- **Plan Phase**: Validates and previews Terraform changes on pull requests
- **Apply Phase**: Applies infrastructure changes on merge to main branch

## Deployment Flow

1. Infrastructure changes are proposed via pull requests in the `terraform-iac` repository
2. GitHub Actions validates the Terraform configuration and shows a plan
3. Upon merge, infrastructure is provisioned/applied
4. Application code changes trigger container builds and ECR pushes
5. ArgoCD can be configured to deploy application manifests from Git repositories

## Future Improvements

* Provision multiple deployment environments (***dev, stage, prod***; at the very least ***dev & prod***)
* Deploy an actual DB for the server (currently in-memmory) and perhaps some `React` client hosted on `S3` for a more complete full-stack project
* Fix incorrect `taint`s on `ArgoCD` pods so they are deployed only on the `argocd` EKS node group and no other pods are deployed on it

