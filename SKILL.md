name: rapid-architect
version: 1.0.0
description: AI-powered architect for DevOps tasks, automating infrastructure design, deployment pipelines, and cloud resource optimization
author: OpenClaw Team
tags: [devops, ai, automation, infrastructure, cloud, cicd]
dependencies: [docker, terraform, ansible, kubectl, awscli, gcloud]
environment_vars:
  - RAPID_ARCHITECT_API_KEY: Required for AI model access
  - CLOUD_PROVIDER: aws|gcp|azure (default: aws)
  - INFRA_MODE: basic|advanced|enterprise (default: basic)
---

# Rapid Architect Skill

## Purpose

Rapid Architect is an AI-powered DevOps architect that automates the design and implementation of infrastructure solutions. It excels at transforming high-level requirements into production-ready DevOps configurations, focusing on cloud-native architectures, CI/CD pipelines, and infrastructure as code.

### Real Use Cases

- **Microservices Migration**: Automatically design Kubernetes manifests and service meshes for migrating monolithic apps to microservices architecture
- **Multi-Cloud Cost Optimization**: Analyze current cloud usage across AWS, GCP, and Azure to recommend optimal resource allocation and cost-saving strategies
- **Zero-Downtime Deployment Pipelines**: Create GitOps workflows with ArgoCD and Flux for blue-green deployments
- **Security-Enhanced Infrastructure**: Implement CIS-compliant VPC configurations with automated security group rules and WAF setups
- **Serverless Architecture Design**: Generate CloudFormation/SAM templates for API Gateway + Lambda + DynamoDB stacks with proper IAM roles
- **Container Orchestration Scaling**: Design horizontal pod autoscaling policies and cluster autoscaling configurations for high-traffic applications

## Scope

### Supported Commands

- `rapid-architect design [blueprint]` - Generate infrastructure blueprints from natural language descriptions
- `rapid-architect deploy --provider=aws --env=prod` - Deploy generated infrastructure to specified cloud provider
- `rapid-architect optimize --target=cost --budget=5000` - Analyze and optimize existing infrastructure for cost/performance
- `rapid-architect audit --compliance=cis --output=json` - Audit infrastructure against security/compliance standards
- `rapid-architect migrate --source=monolith --target=k8s` - Generate migration plans and scripts
- `rapid-architect monitor --alerts=slack --threshold=80` - Set up monitoring dashboards and alerting rules

### Environment Support

- AWS (EC2, ECS, EKS, Lambda, S3, RDS)
- GCP (GCE, GKE, Cloud Functions, BigQuery)
- Azure (VMs, AKS, Functions, CosmosDB)
- Kubernetes (vanilla, EKS, GKE, AKS)
- Docker Compose and Swarm
- Terraform and CloudFormation

## Work Process

### Step 1: Requirements Analysis
- Parse natural language input for infrastructure needs
- Identify required cloud services, scaling requirements, and compliance needs
- Generate dependency graph and resource estimation

### Step 2: Blueprint Generation
- Create Terraform/CloudFormation templates with proper resource naming
- Implement security best practices (least privilege IAM, encrypted storage)
- Add monitoring integrations (CloudWatch, Stackdriver, Application Insights)

### Step 3: Validation and Testing
- Run terraform validate and plan commands
- Simulate deployment in isolated environment
- Verify compliance with chosen standards (SOC2, HIPAA, PCI-DSS)

### Step 4: Deployment Execution
- Apply infrastructure changes with proper state management
- Configure CI/CD pipelines for future deployments
- Set up automated backups and disaster recovery

### Step 5: Optimization and Monitoring
- Implement cost allocation tags and budget alerts
- Configure auto-scaling policies based on usage patterns
- Establish monitoring dashboards with key performance indicators

## Golden Rules

1. **Never deploy to production without dry-run validation** - Always execute `terraform plan` before `apply`
2. **Implement least privilege access** - Generate IAM policies that grant minimum required permissions
3. **Encrypt data at rest and in transit** - Use KMS keys for storage encryption and TLS 1.3 for all communications
4. **Version control all infrastructure code** - Store Terraform states in S3/GCS with locking enabled
5. **Monitor everything, alert on anomalies** - Implement comprehensive logging and set up PagerDuty integrations
6. **Cost optimization over feature creep** - Prioritize efficient resource usage over unnecessary capabilities
7. **Zero-trust networking** - Implement network segmentation with security groups and firewall rules
8. **Immutable infrastructure** - Design for containerized, stateless applications with rolling updates

## Examples

### Example 1: Designing a Microservices Architecture

**Input:**
```
rapid-architect design "Create a microservices architecture for an e-commerce platform with user auth service, product catalog, order management, and payment processing. Use AWS EKS, PostgreSQL RDS, Redis cache, and implement blue-green deployments"
```

**Output:**
```yaml
# Generated infrastructure.yaml
version: '3.8'
services:
  auth-service:
    image: auth-service:latest
    environment:
      - DB_HOST=postgres.rds.amazonaws.com
      - REDIS_URL=redis.elasticache.amazonaws.com
    deploy:
      replicas: 3
      resources:
        limits:
          memory: 512M
        reservations:
          memory: 256M
    networks:
      - microservices-net

  product-service:
    image: product-service:latest
    environment:
      - DB_HOST=postgres.rds.amazonaws.com
    deploy:
      replicas: 5
    networks:
      - microservices-net

networks:
  microservices-net:
    driver: overlay

# Generated terraform/main.tf
resource "aws_eks_cluster" "microservices" {
  name     = "microservices-cluster"
  role_arn = aws_iam_role.eks_cluster.arn
  vpc_config {
    subnet_ids = aws_subnet.private[*].id
  }
}

resource "aws_db_instance" "postgres" {
  allocated_storage    = 20
  engine               = "postgres"
  engine_version       = "13.7"
  instance_class       = "db.t3.micro"
  name                 = "microservices_db"
  username             = "admin"
  password             = var.db_password
  parameter_group_name = "default.postgres13"
  skip_final_snapshot  = true
  vpc_security_group_ids = [aws_security_group.rds.id]
}
```

### Example 2: Cost Optimization Analysis

**Input:**
```
rapid-architect optimize --target=cost --budget=3000 --provider=aws --region=us-east-1
```

**Output:**
```
=== Cost Optimization Report ===
Current Monthly Cost: $4,250
Target Budget: $3,000
Optimization Potential: 29%

Recommendations:
1. Rightsize EC2 instances: t3.large → t3.medium (Save $450/month)
2. Use Spot instances for dev environments (Save $300/month)
3. Implement S3 Intelligent Tiering (Save $200/month)
4. Reserved Instances for production workloads (Save $600/month)

Generated terraform/optimization.tf:
resource "aws_instance" "web_server" {
  ami           = "ami-0c55b159cbfafe1d0"
  instance_type = "t3.medium"  # Changed from t3.large
  # ... other config
}
```

### Example 3: Security Audit

**Input:**
```
rapid-architect audit --compliance=cis --output=json --provider=aws
```

**Output:**
```json
{
  "audit_id": "cis-aws-2024-001",
  "compliance_level": "CIS AWS Foundations Benchmark v1.5",
  "total_checks": 45,
  "passed": 38,
  "failed": 7,
  "critical_failures": 2,
  "recommendations": [
    {
      "check_id": "1.3",
      "severity": "HIGH",
      "description": "Ensure credentials unused for 90 days or greater are disabled",
      "resource": "iam_user.stale_user",
      "remediation": "aws iam delete-access-key --user-name stale_user --access-key-id AKIOLDKEY123"
    },
    {
      "check_id": "2.1.1",
      "severity": "MEDIUM",
      "description": "Ensure S3 Bucket Policy allows HTTPS requests only",
      "resource": "aws_s3_bucket.public_bucket",
      "remediation": "Add condition to bucket policy: {\"StringEquals\": {\"aws:SecureTransport\": \"true\"}}"
    }
  ]
}
```

## Rollback Commands

### Infrastructure Rollback
- `terraform destroy -target=aws_instance.web_server` - Remove specific resource
- `kubectl rollout undo deployment/auth-service --to-revision=2` - Rollback Kubernetes deployment
- `aws cloudformation delete-stack --stack-name microservices-stack` - Delete entire CloudFormation stack

### Configuration Rollback
- `git revert HEAD~3..HEAD` - Revert last 3 infrastructure commits
- `ansible-playbook rollback.yml -i inventory/prod` - Execute rollback playbook
- `kubectl apply -f backup-configmap.yaml` - Restore previous configuration

### State Recovery
- `terraform state mv aws_instance.old aws_instance.new` - Rename resources in state
- `kubectl get configmap app-config -o yaml > backup.yaml` - Backup current config
- `aws s3 cp s3://terraform-state-backup/state.tfstate .terraform/terraform.tfstate` - Restore state from backup

## Dependencies and Requirements

### System Requirements
- Docker Engine 20.10+
- Terraform 1.5.0+
- Ansible 2.15+
- kubectl 1.27+
- Python 3.9+ with boto3, kubernetes, and terraform-python libraries

### API Access
- Cloud provider CLI tools configured with appropriate permissions
- AI model API key for architecture suggestions
- Monitoring service integrations (Datadog, New Relic, Prometheus)

### Network Requirements
- Outbound HTTPS access to cloud provider APIs
- VPN or direct connect for private cloud resources
- DNS resolution for internal service discovery

## Verification Steps

1. **Syntax Validation**: Run `terraform validate` and `terraform fmt` on generated code
2. **Plan Review**: Execute `terraform plan` and review proposed changes
3. **Cost Estimation**: Use `infracost` tool to estimate deployment costs
4. **Security Scan**: Run `checkov` or `terrascan` for policy violations
5. **Integration Testing**: Deploy to staging environment and run automated tests
6. **Performance Benchmarking**: Use `terraformer` to baseline current infrastructure
7. **Compliance Audit**: Generate reports using `cloud-custodian` policies

## Troubleshooting

### Common Issues

**Issue: Terraform state lock errors**
```
Error: Error acquiring the state lock
```
**Solution:** Force unlock with `terraform force-unlock LOCK_ID` after verifying no other processes are running

**Issue: Kubernetes API server unreachable**
```
Unable to connect to the server: dial tcp: lookup api.k8s.cluster.local on 8.8.8.8:53: no such host
```
**Solution:** Update kubeconfig with correct cluster endpoint and authenticate with `aws eks update-kubeconfig`

**Issue: Cloud provider rate limiting**
```
Error: Request rate exceeded
```
**Solution:** Implement exponential backoff in deployment scripts or use `--max-retries=10` flags

**Issue: Cost overruns despite optimization**
```
Monthly bill: $5,200 (Target: $3,000)
```
**Solution:** Enable AWS Budgets alerts and implement `aws-nuke` for unused resource cleanup

**Issue: Deployment failures in CI/CD**
```
Pipeline failed: Resource creation timeout
```
**Solution:** Increase timeout values in Terraform configurations and implement retry logic with `local-exec` provisioners

**Issue: Security group misconfigurations**
```
Error: Invalid security group rules
```
**Solution:** Use `aws ec2 describe-security-groups` to audit rules and regenerate with proper CIDR blocks