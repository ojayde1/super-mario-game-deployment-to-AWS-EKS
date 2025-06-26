# Super Mario Game Deployment to AWS EKS

This repository contains Infrastructure as Code (IaC) and Kubernetes manifests to deploy a Super Mario Bros game to Amazon Elastic Kubernetes Service (EKS) using Terraform and kubectl.

## 🎮 Project Overview

This project demonstrates a complete DevOps pipeline for deploying a containerized Super Mario Bros game on AWS EKS. It includes:
- Terraform configuration for EKS cluster provisioning
- Kubernetes deployment and service manifests
- Automated setup script for required tools
- Load balancer configuration for external access

## 📁 Repository Structure

```
.
├── EKS-TF/
│   ├── main.tf          # Main Terraform configuration for EKS cluster
│   └── provider.tf      # AWS provider configuration
├── deployment.yaml      # Kubernetes deployment manifest
├── service.yaml         # Kubernetes service manifest (LoadBalancer)
├── script.sh           # Setup script for tools installation
├── LICENSE             # MIT License
└── README.md           # This file
```

## 🛠️ Prerequisites

- AWS Account with appropriate permissions
- AWS CLI configured with credentials
- Terraform >= 1.0
- kubectl
- Linux/Unix environment (for script execution)

## 🚀 Quick Start

### 1. Install Required Tools

Run the provided setup script to install Terraform, kubectl, and AWS CLI:

```bash
chmod +x script.sh
./script.sh
```

### 2. Configure AWS Credentials

```bash
aws configure
```

Provide your AWS Access Key ID, Secret Access Key, and preferred region.

### 3. Deploy EKS Cluster

Navigate to the Terraform directory and deploy the infrastructure:

```bash
cd EKS-TF
terraform init
terraform plan
terraform apply
```

### 4. Configure kubectl

Update your kubeconfig to connect to the new EKS cluster:

```bash
aws eks update-kubeconfig --region us-east-2 --name EKS_CLOUD
```

### 5. Deploy the Super Mario Game

Apply the Kubernetes manifests:

```bash
kubectl apply -f deployment.yaml
kubectl apply -f service.yaml
```

### 6. Access the Game

Get the LoadBalancer URL:

```bash
kubectl get service mario-service
```

Wait for the EXTERNAL-IP to be assigned, then access the game via the provided URL.

## 📋 Component Details

### Terraform Configuration

**EKS Cluster (`main.tf`):**
- Creates EKS cluster named "EKS_CLOUD"
- Configures IAM roles and policies
- Sets up node group with t2.medium instances
- Uses default VPC and public subnets
- Scaling configuration: 1-2 nodes

**Provider (`provider.tf`):**
- AWS provider version ~> 5.0
- Default region: us-east-2

### Kubernetes Manifests

**Deployment (`deployment.yaml`):**
- Deploys Super Mario game using `sevenajay/mario:latest` image
- 2 replicas for high availability
- Container port: 80

**Service (`service.yaml`):**
- LoadBalancer type service
- Exposes the game on port 80
- Provides external access via AWS Load Balancer

### Setup Script (`script.sh`)

Automated installation of:
- Terraform (latest version)
- kubectl (latest stable version)
- AWS CLI v2

## 🔧 Configuration Options

### Scaling the Application

Modify the `replicas` field in `deployment.yaml`:

```yaml
spec:
  replicas: 3  # Increase for more instances
```

### Changing Instance Types

Update the `instance_types` in `main.tf`:

```hcl
instance_types = ["t3.medium"]  # Change instance type
```

### Regional Deployment

Update the region in `provider.tf` and ensure you have appropriate VPC/subnet configuration.

## 🧹 Cleanup

To avoid AWS charges, clean up resources when done:

```bash
# Delete Kubernetes resources
kubectl delete -f service.yaml
kubectl delete -f deployment.yaml

# Destroy Terraform infrastructure
cd EKS-TF
terraform destroy
```

## 🔍 Troubleshooting

### Common Issues

1. **EKS Cluster Creation Fails**
   - Verify AWS credentials and permissions
   - Check if default VPC exists in the region

2. **kubectl Connection Issues**
   - Ensure kubeconfig is updated: `aws eks update-kubeconfig --region us-east-2 --name EKS_CLOUD`
   - Verify cluster status: `aws eks describe-cluster --name EKS_CLOUD`

3. **LoadBalancer Pending**
   - Wait 5-10 minutes for AWS to provision the load balancer
   - Check security groups allow traffic on port 80

### Useful Commands

```bash
# Check cluster status
kubectl cluster-info

# View pods
kubectl get pods

# Check service status
kubectl get svc mario-service

# View logs
kubectl logs -l app=mario
```

## 💰 Cost Considerations

- EKS cluster: ~$0.10/hour
- EC2 instances (t2.medium): ~$0.0464/hour per instance
- Load Balancer: ~$0.0225/hour
- Data transfer charges may apply

**Estimated monthly cost: ~$50-70 USD**

## 🤝 Contributing

Contributions are welcome! Please feel free to submit a Pull Request.

## 📄 License

This project is licensed under the MIT License - see the [LICENSE](LICENSE) file for details.

## 🙏 Acknowledgments

- Super Mario game image: `sevenajay/mario:latest`
- AWS EKS documentation
- Terraform AWS provider documentation
