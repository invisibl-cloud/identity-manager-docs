# Getting started

identity-manager operator runs in kubernetes cluster and manages WorkloadIdentity resources.

> Note: The minimum supported version of Kubernetes is `1.16.0`.

## Installing with Helm

``` bash
helm repo add invisibl https://charts.invisibl.io

helm install identity-manager \
   invisibl/identity-manager \
    -n identity-manager \
    --create-namespace
```


### Create a secret containing your AWS credentials

```shell
echo -n 'KEYID' > ./access-key
echo -n 'SECRETKEY' > ./secret-access-key
kubectl create secret generic aws-credentials --from-file=./access-key  --from-file=./secret-access-key
```

### Create your first WorkloadIdentity

``` yaml
--8<-- "examples/basic-workload-identity.yaml"
```

For more advanced examples, please read the other
[guides](guides-introduction.md).


### Uninstalling with Helm

Uninstall the helm release using the delete command.

```bash
helm delete identity-manager --namespace identity-manager
```
