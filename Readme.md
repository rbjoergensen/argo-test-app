# ArgoCD testing
## Notes
Connect to unexposed ArgoCD instance in EKS
``` powershell
# Get own identity
aws sts get-caller-identity
# Assume the role of the cluster admin
aws sts assume-role --role-arn arn:aws:iam::000000000000:role/eks-cluster-admin-dev --role-session-name aws-cli-session
# Setup the context to our EKS cluster
aws eks update-kubeconfig --region eu-central-1 --name cluster01
# Port forward port 443 from the argocd-server service
kubectl port-forward svc/argocd-server -n argocd 8080:443
```
Assume a role in PowerShell.
``` powershell
$region = "eu-west-1"
$roleArn = "arn:aws:iam::000000000000:role/eks-cluster-admin-dev"
$assumeRole = aws sts assume-role --role-arn $roleArn --role-session-name aws-cli-session | ConvertFrom-Json
$ENV:AWS_REGION = $region
$ENV:AWS_ACCESS_KEY_ID = $assumeRole.Credentials.AccessKeyId
$ENV:AWS_SECRET_ACCESS_KEY = $assumeRole.Credentials.SecretAccessKey
$ENV:AWS_SESSION_TOKEN = $assumeRole.Credentials.SessionToken
```
``` powershell
helm package helm/templates
aws ecr create-repository --repository-name argo-test-app --region eu-central-1
$env:HELM_EXPERIMENTAL_OCI=1
aws ecr get-login-password --region eu-central-1 | helm registry login --username AWS --password-stdin 181095382557.dkr.ecr.eu-central-1.amazonaws.com/
helm push argo-test-app-0.0.3.tgz oci://181095382557.dkr.ecr.eu-central-1.amazonaws.com/
aws ecr describe-images --repository-name argo-test-app --region eu-central-1
```
## Links
- https://argo-cd.readthedocs.io/en/stable/getting_started/
