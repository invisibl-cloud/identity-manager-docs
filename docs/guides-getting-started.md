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
{% include 'basic-workload-identity.yaml' %}
```

``` bash
kubectl describe workloadidentity demoapp-v1
# [...]
Name:  example
Status:
  Conditions:
    Last Transition Time:  2021-02-24T16:45:23Z
    Message:               Secret was synced
    Reason:                SecretSynced
    Status:                True
    Type:                  Ready
  Refresh Time:            2021-02-24T16:45:24Z
Events:                    <none>
```

For more advanced examples, please read the other
[guides](guides-introduction.md).


### Uninstalling with Helm

Uninstall the helm release using the delete command.

```bash
helm delete identity-manager --namespace identity-manager
```
