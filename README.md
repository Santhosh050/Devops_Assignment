# Devops_Assignment

# Objective:
Deploy a CI/CD pipeline on AWS using Terraform (VPC + EKS), Kubernetes (NGINX app), and ArgoCD (GitOps).

# Prerequisites
AWS CLI installed & configured with a profile that has privileges to create VPC, EKS, IAM, ELB, etc.
kubectl and terraform installed locally.
GitHub repo with manifests/ and argocd/ folders (ArgoCD app YAML in argocd/argocdapp.yaml).
Region used: us-east-1 (adjust commands if different).

# Steps:
# 1) Initialize & Deploy Infrastructure using Terraform
#   Navigate to the terraform directory and run the below commands:
#     1. Change to terraform directory
        cd terraform
#     2. Initialize the directory with terraform
        terraform init
#     3.Validate the terraform syntax
        terraform validate
#     4.verify the resources that will be ceated using this command:
        terraform plan
#     5.Run apply command to create resources
        terraform apply -auto-approve
#     6.Once deployed, update kubeconfig by running this command:
        aws eks update-kubeconfig --name appscrip-eks --region us-east-1
#     7.Verify the created nodes using below command(Optional):
        kubectl get nodes

# 2) Install ArgoCD on EKS
#   1. Create an namespace for ardocd:
        kubectl create namespace argocd
#   2. Run below command to create resources in argocd namespace:
        kubectl apply -n argocd -f https://raw.githubusercontent.com/argoproj/argo-cd/stable/manifests/install.yaml
#    3.Check status:
        kubectl get pods -n argocd

# 3) Access ArgoCD UI using Port forward ArgoCD service:
    kubectl port-forward svc/argocd-server -n argocd 8080:443
# Open in browser:
    https://localhost:8080
# Get admin password:
    kubectl -n argocd get secret argocd-initial-admin-secret -o jsonpath="{.data.password}" | base64 --decode
# Login using below:
    Username: admin
    Password: (output from above)

# Configure ArgoCD for GitOps
  # Apply ArgoCD Application config:
    (Note: Ensure Github repo details are updated in argocdapp.yaml)
        kubectl apply -f argocd/argocdapp.yaml
