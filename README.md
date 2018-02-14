# Demo app with HA Kubernetes on AWS

This repo is a demonstration of the bootstrap of full operating Kubernetes cluster in HA and MulitAZ using a Hello World app on AWS.

## Prerequisite

1. aws cli with IAM access to create IAM users and groups
1. kops
1. kubectl

## Bootstrap

The easiest way to bootstrap the Kubernetes cluster is using the helpers in the bin directory.

First of all you must set your own parameters at the config file etc/vars.conf where variables like the cluster name, the AWS region or the dns zone to be used by k8s are defined.

Execute helpers following this order:

1. iam_permissions_kops
1. r53_zone_setup
1. s3_bucket
1. start_k8s_cluster
1. create_k8s_resources


## Topology

### AWS
- New vpc created by default
- 3 private subnets (with route tables pointing to a bastion)
- 3 public subnets (named utility subnets)
- AutoScalingGroup for Bastion nodes
- AutoScalingGroup for master (min of 1 instance, max of 1) on AZ-a 
- AutoScalingGroup for master (min of 1 instance, max of 1) on AZ-b
- AutoScalingGroup for master (min of 1 instance, max of 1) on AZ-c
- AutoScalingGroup for minions (min of 2, in multi AZ)
- ELB from k8s ingress service (svc type LoadBalancer with an HTTP listener, port 80)

### Kubernetes

- Namespace: demo
- ConfigMap for Nginx ingress controller
- Deployments: 
    - default-http-backend: nginx ingress default 404, 2 replicas
    - nginx-ingress-controller: nginx ingress reverse proxies, min of 2 replicas
    - demo-app: NodeJS application (Hello World), min of 2 replicas (for demo purposes as a hello world doesn't need autoscaling)
- Services:
    - default-http-backend: Internal to k8s
    - ingress-nginx: type LoadBalancer, mapping to an ELB listening on port 80 (You can add ssl with annotations)
    - demo-app: Internal to k8s
- Ingress:
    - demo-ingress: Forwards all HTTP traffic to demo-app service. Even though, an ingress rule and controller is not necessary for a hello world app, the design focus on scale both applications and resources if needed. With ingress rules we enable different DNS names using the same ELB to access the cluster.
- Application POD:
    - The NodeJS pod spec is defined in the deployment. The docker image used can be found on the repository arau/demo-nodejs. You can find the details in the docker directory of the project.

## Considerations

The cluster doesn't add system components like logging aggregation, public DNS auto update or monitoring as any production cluster should implement because of the purpose of the repo.
