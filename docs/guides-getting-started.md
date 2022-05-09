# Getting started

This guide helps you to setup the cluster, install Identity Manager, deploy a workload identity and verify the working with a demo application. The following installation steps are applicable for AWS EKS cluster.

> Note: The minimum supported version of Kubernetes is `1.16.0`.

## Create a cluster

The following instructions allows you to create a cluster in AWS EKS. They also takes care of creation of a namespace, service account and creation of a role with the IAM policies that are required by the Identity Manager and binds the role to the service account to be created.

> Note: If you already have a cluster ready, you can skip this section and refer to [Working with an existing cluster section](#working-with-an-existing-cluster).

// (TODO: Check if subnet is fixed or dynamic, remove nodegroup in yaml)

1. The following eks config file helps you to create a cluster with the required IAM role and policies, an m5.large node in the `us-east-1` region. The mentioned policies are the minimum required permissions for Identity Manager. You can change the other config as per your preference. Save the following yaml as `eks-identity-manager.yaml`.
``` yaml
--8<-- "examples/eks-identity-manager.yaml"
```
2. Apply the yaml:  
`eksctl create cluster -f eks-identity-manager.yaml`

The EKS cluster should have been created.

## Working with an existing cluster
For an existing cluster, a new namespace, a service account and an IAM role need to be created. A few policies that the Identity Manager needs for its working need to be attached to the newly created role.

1. Create the namespace and service account
```bash
kubectl create namespace identity-manager
kubectl create serviceaccount identity-manager -n identity-manager
```

2. export a few required values:
```bash
export ACCOUNT_ID=$(aws sts get-caller-identity --query "Account" --output text)

export OIDC_PROVIDER=$(aws eks describe-cluster --name identity-manager-test --query "cluster.identity.oidc.issuer" --region us-east-1 --output text | sed -e "s/^https:\/\///")

export NAMESPACE=identity-manager
export SERVICEACCNAME=identity-manager
export ROLENAME=identity-manager
```

3. Save the required IAM polices:
```bash
read -r -d '' IDENTITY_POLICIES <<EOF
{
    "Version": "2012-10-17",
    "Statement": [
        {
            "Action": [
                "iam:AttachRolePolicy",
                "iam:CreateRole",
                "iam:DeleteRole",
                "iam:DeleteRolePolicy",
                "iam:DetachRolePolicy",
                "iam:GetRole",
                "iam:ListAttachedRolePolicies",
                "iam:ListRolePolicies",
                "iam:PutRolePolicy",
                "iam:UpdateAssumeRolePolicy",
                "iam:UpdateRole",
                "sts:GetCallerIdentity"
            ],
            "Resource": "*",
            "Effect": "Allow"
        }
    ]
}
EOF
echo "${IDENTITY_POLICIES}" > identity-policies.json
```

4. Save the trust relationship
```bash
read -r -d '' TRUST_RELATIONSHIP <<EOF
{
  "Version": "2012-10-17",
  "Statement": [
    {
      "Effect": "Allow",
      "Principal": {
        "Federated": "arn:aws:iam::${ACCOUNT_ID}:oidc-provider/${OIDC_PROVIDER}"
      },
      "Action": "sts:AssumeRoleWithWebIdentity",
      "Condition": {
        "StringEquals": {
          "${OIDC_PROVIDER}:aud": "sts.amazonaws.com",
          "${OIDC_PROVIDER}:sub": "system:serviceaccount:${NAMESPACE}:${SERVICEACCNAME}"
        }
      }
    }
  ]
}
EOF
echo "${TRUST_RELATIONSHIP}" > trust.json
```

5. Create the IAM role:
```bash
aws iam create-role --role-name ${ROLENAME} --assume-role-policy-document file://trust.json --description "identity-role"
```
Note down the IAM role ARN in the response.

6. Attach the IAM policies to the role:
```bash
aws iam put-role-policy --role-name <ROLENAME> --policy-name identity-policy --policy-document file://identity-policies.json
```

7. Annotate the service account with the IAM role ARN that was noted in the step 5.
```bash
kubectl annotate serviceaccount -n identity-manage identity-manage \
eks.amazonaws.com/role-arn=<ROLE ARN>
```

Now the service account `identity-manager` is ready for the Identity Manager installation.

## Installing Identity Manager with Helm


1. Copy the cluster's IAM role:
If the cluster is newly created using `eksctl`, copy the IAM role ARN of the cluster from CloudFormation template. The IAM role can be found in the annotation of the service account which is created during the cluster creation. If the role has been created for an existing cluster, copy the created role's ARN.
``` bash
export IAM_ROLE=<IAM role>
```
// TODO: installing on sa created by eksctl will have managed-by label as "eksctl" instead of "helm" and this `helm uninstall` not work and the deployment has to be manually deleted.
2. Install Idenity Manager
``` bash
helm repo add invisibl https://charts.invisibl.io

helm install invisibl/identity-manager  --set provider.aws.enabled=true --set provider.aws.arn=$IAM_ROLE --set serviceAccount.create=false --set serviceAccount.name=identity-manager --namespace=identity-manager --generate-name
```

## Create your first WorkloadIdentity

1. Get your AWS account ID
```bash
ACCOUNT_ID=$(aws sts get-caller-identity --query "Account" --output text)
```

2. Get your OIDC provider
```bash
OIDC_PROVIDER=$(aws eks describe-cluster --name identity-manager-test --query "cluster.identity.oidc.issuer" --region us-east-1 --output text | sed -e "s/^https:\/\///")
```

3. Create a demo namespace for workload identity deployment
```bash
kubectl create namespace demo
```

4. Save the following workload identity yaml in `demo-workload-identity.yaml`. Replace the `ACCOUNT_ID` and `OIDC_PROVIDER` in the yaml with the values obtained in the previous steps.
``` yaml
--8<-- "examples/demo-identity-workload-placeholder.yaml"
```

5. Apply the workload identity.
``` bash
kubectl apply -f demo-workload-identity.yaml
```

The above workload identity is deployed in the namespace `demo`. The identity manager will create an IAM role `demo-identity` in AWS with the inline polices mentioned in the workload identity attached to the IAM role. The identity manager will also create a service account `sa-demo` and will annotate it with the newly created role to facilitate the role binding.
6. Verify the role binding for service account
``` bash
kubectl get serviceaccount sa-demo -n demo  -o=jsonpath='{.metadata.annotations}'
```

The response should look similar to the below one:
``` bash
{"eks.amazonaws.com/role-arn":"arn:aws:iam::<Account ID>:role/demo-identity"}
```

Now the service account is ready to provide the necessary permissions for the deployments.

## Deploy a demo application

// TODO: Publish the demo app helm chart
``` bash
helm install invisible/idm-test-tool --set serviceAccount.name=sa-demo --namespace=demo --generate-name
```

Check the logs of the demo application pod and it should list your EC2 instances using the new role 
`demo-identity`.

``` bash
time="2022-05-04T09:54:04Z" level=info msg="STS:"
time="2022-05-04T09:54:04Z" level=info msg="STS ARN: arn:aws:sts::<Account ID>:assumed-role/demo-identity/48520678504627062014"
time="2022-05-04T09:54:04Z" level=info msg="EC2:"
time="2022-05-04T09:54:04Z" level=info msg="Reservation ID: r-0532a81dd8ed78de1"
time="2022-05-04T09:54:04Z" level=info msg="Instance ID: i-078e85384f15b27b9"
time="2022-05-04T09:54:04Z" level=info msg="Reservation ID: r-0ec5be0a1e1017088"
time="2022-05-04T09:54:04Z" level=info msg="Instance ID: i-0efee718f18c10742"
time="2022-05-04T09:54:04Z" level=info msg="Reservation ID: r-0992e8b92ae857ddd"
time="2022-05-04T09:54:04Z" level=info msg="Instance ID: i-097b6eb735190898c"
time="2022-05-04T09:54:04Z" level=info msg="Reservation ID: r-0f7c4e3a8d62c0af7"
time="2022-05-04T09:54:04Z" level=info msg="Instance ID: i-09e2a542b827858de"
time="2022-05-04T09:54:04Z" level=info msg="Instance ID: i-0c77422d4e56c42c9"
time="2022-05-04T09:54:04Z" level=info msg="Reservation ID: r-0265da1370d12b44d"
time="2022-05-04T09:54:04Z" level=info msg="Instance ID: i-05d02af34a271e308"   
time="2022-05-04T09:54:04Z" level=info msg="Reservation ID: r-0efa1b0178917b544"
time="2022-05-04T09:54:04Z" level=info msg="Instance ID: i-04027edd12c82f6d6"
time="2022-05-04T09:54:04Z" level=info msg="Reservation ID: r-0b0d08c57fb60bb71"
time="2022-05-04T09:54:04Z" level=info msg="Instance ID: i-09d4114c468def93e"
time="2022-05-04T09:54:04Z" level=info msg="Reservation ID: r-09e8cc98f64abef83"
time="2022-05-04T09:54:04Z" level=info msg="Instance ID: i-0bc14ad13ef223d76"
time="2022-05-04T09:54:04Z" level=info
time="2022-05-04T09:54:04Z" level=info msg="Reservations count: 8"
time="2022-05-04T09:54:04Z" level=info msg="Instances count: 9"

```

### Uninstalling with Helm

// TODO: Resolve helm vs eks conflict in sa annotation
Uninstall the helm release using the delete command.

```bash
helm delete identity-manager --namespace identity-manager
```
