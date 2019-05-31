# Setting up AWS EKS (Hosted Kubernetes)

See https://www.terraform.io/docs/providers/aws/guides/eks-getting-started.html for full guide


## Download kubectl
```
curl -LO https://storage.googleapis.com/kubernetes-release/release/$(curl -s https://storage.googleapis.com/kubernetes-release/release/stable.txt)/bin/linux/amd64/kubectl
chmod +x kubectl
sudo mv kubectl /usr/local/bin
```

## Download the aws-iam-authenticator
```
wget https://github.com/kubernetes-sigs/aws-iam-authenticator/releases/download/v0.3.0/heptio-authenticator-aws_0.3.0_linux_amd64
chmod +x heptio-authenticator-aws_0.3.0_linux_amd64
sudo mv heptio-authenticator-aws_0.3.0_linux_amd64 /usr/local/bin/heptio-authenticator-aws
```

## Modify providers.tf

Choose your region. EKS is not available in every region, use the Region Table to check whether your region is supported: https://aws.amazon.com/about-aws/global-infrastructure/regional-product-services/

Make changes in providers.tf accordingly (region, optionally profile)

## Terraform apply
```
terrafomr init
terraform apply
```

## Configure kubectl
```
terraform output kubeconfig-cluster1 # save output in ~/.kube/config
terraform output kubeconfig-cluster2 # save output in ~/.kube/config
```

## Configure config-map-auth-aws
```
terraform output config-map-aws-auth-cluster1 # save output in config-map-aws-auth-cluster1.yaml
kubectl apply -f config-map-aws-auth.yaml

terraform output config-map-aws-auth-cluster2 # save output in config-map-aws-auth-cluster2.yaml
kubectl apply -f config-map-aws-auth.yaml
```

## See nodes coming up
```
kubectl get nodes
```

## Sample Test
```
kubectl run hellocluster1 --image=k8s.gcr.io/echoserver:1.4 --port=8080
```

## To create the second EKS cluster
```
Rename eks-cluster2.tf.rename to eks-cluster2.tf
Rename eks-workers2.tf.rename to eks-workers2.tf
Rename eks-outputs2.tf.rename to eks-outputs2.tf
Run terraform plan; terraform apply
```

## Destroy
Make sure all the resources created by Kubernetes are removed (LoadBalancers, Security groups), and issue:
```
terraform destroy
```
