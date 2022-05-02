# API Reference

## WorkloadIdentity

WorkloadIdentity is the schema for the workloadload identity API

<table>
    <thead>
        <tr>
            <th>Name</th>
            <th>Type</th>
            <th>Description</th>
            <th>Required</th>
        </tr>
    </thead>
    <tbody><tr>
      <td><b>apiVersion</b></td>
      <td>string</td>
      <td>identity-manager.io/v1alpha1</td>
      <td>true</td>
      </tr>
      <tr>
      <td><b>kind</b></td>
      <td>string</td>
      <td>WorkloadIdentity</td>
      <td>true</td>
      </tr>
      <tr>
      <td><b>metadata</b></td>
      <td>object</td>
      <td>Refer to the Kubernetes API documentation for the fields of the `metadata` field.</td>
      <td>true</td>
      </tr><tr>
        <td><b><a href="#workloadidentityspec">spec</a></b></td>
        <td>object</td>
        <td>
          WorkloadIdentitySpec defines the desired state of WorkloadIdentity<br/>
        </td>
        <td>true</td>
      </tr><tr>
        <td><b>status</b></td>
        <td>object</td>
        <td>
          Status is auto populated after the deployment of workload identity.
        </td>
        <td>false</td>
      </tr></tbody>
</table>

### WorkloadIdentity.spec

WorkloadIdentitySpec defines the desired state of WorkloadIdentity

<table>
    <thead>
        <tr>
            <th>Name</th>
            <th>Type</th>
            <th>Description</th>
            <th>Required</th>
        </tr>
    </thead>
    <tbody><tr>
        <td><b>name</b></td>
        <td>string</td>
        <td>
          Name of the workloadidentity
        </td>
        <td>false</td>
      </tr><tr>
      </tr><tr>
        <td><b>description</b></td>
        <td>string</td>
        <td>
          Description of the workload identity
        </td>
        <td>false</td>
      </tr><tr>
        <td><b>credentials</b></td>
        <td>object</td>
        <td>
          Cloud credentials to manage workload identity
        </td>
        <td>true</td>
      </tr><tr>
        <td><b>provider</b></td>
        <td>string</td>
        <td>
          Specifies the cloud provider. It can be either 'AWS' or 'Azure'
        </td>
        <td>true</td>
      </tr><tr>
        <td><b><a href="#workloadidentity.spec.aws">aws</a></b></td>
        <td>object</td>
        <td>
          AWS spec definition if the provider is AWS
        </td>
        <td>false</td>
      </tr><tr>
        <td><b><a href="#workloadidentity.spec.azure">azure</a></b></td>
        <td>object</td>
        <td>
          Azure spec definition if the provider is Azure
        </td>
        <td>false</td>
      </tr></tbody>
</table>

### WorkloadIdentity.spec.credentials

Credentials defines cloud credentials to manage workload identity

<table>
    <thead>
        <tr>
            <th>Name</th>
            <th>Type</th>
            <th>Description</th>
            <th>Required</th>
        </tr>
    </thead>
    <tbody><tr>
        <td><b>source</b></td>
        <td>enum</td>
        <td>
          Defines the source of the credentials.
        </td>
        <td>false</td>
      </tr><tr>
        <td><b>secretRef</b></td>
        <td>object</td>
        <td>
          Defines the reference to fetch the credentials
        </td>
        <td>false</td>
      </tr><tr>
        <td><b>properties</b></td>
        <td>map[string][string]</td>
        <td>
          Defines the properties of credentials. This is used to override the properties stored in the secret.
        </td>
        <td>false</td>
      </tr></tbody>
</table>

### WorkloadIdentity.spec.aws

WorkloadIdentityAWS defines the AWS policies

<table>
    <thead>
        <tr>
            <th>Name</th>
            <th>Type</th>
            <th>Description</th>
            <th>Required</th>
        </tr>
    </thead>
    <tbody><tr>
        <td><b>path</b></td>
        <td>string</td>
        <td>
          Defines the path of the role
        </td>
        <td>false</td>
      </tr><tr>
      </tr><tr>
        <td><b>maxSessionDuration</b></td>
        <td>int64</td>
        <td>
          Defines the maximum duration the session of the role can be active
        </td>
        <td>false</td>
      </tr><tr>
        <td><b>assumeRolePolicy</b></td>
        <td>string</td>
        <td>
          Defines the assume role policies of the role
        </td>
        <td>true</td>
      </tr><tr>
        <td><b>inlinePolicies</b></td>
        <td>string</td>
        <td>
          Defines inline policies of the role
        </td>
        <td>false</td>
      </tr><tr>
        <td><b>policies</b></td>
        <td>[]string</td>
        <td>
          Defines policies of the role
        </td>
        <td>false</td>
      </tr><tr>
        <td><b><a href="#workloadidentity.spec.aws.serviceaccounts">serviceaccounts</a></b></td>
        <td>[]object</td>
        <td>
          Defines the service account to be managed
        </td>
        <td>false</td>
      </tr><tr>
        <td><b>pods</b></td>
        <td>[]object</td>
        <td>
          Defines the pods to be managed
        </td>
        <td>false</td>
      </tr></tbody>
</table>

### WorkloadIdentity.spec.aws.serviceAccounts

ServiceAccount defines the service account for which the role is be effective

<table>
    <thead>
        <tr>
            <th>Name</th>
            <th>Type</th>
            <th>Description</th>
            <th>Required</th>
        </tr>
    </thead>
    <tbody><tr>
        <td><b>action</b></td>
        <td>enum</td>
        <td>
          Defines if service account needs to be created or updated. The possible values are `Create` or `Update` or `` (empty value)
        </td>
        <td>false</td>
      </tr><tr>
      </tr><tr>
        <td><b>name</b></td>
        <td>string</td>
        <td>
          Defines the name of the service account
        </td>
        <td>false</td>
      </tr><tr>
        <td><b>namespace</b></td>
        <td>string</td>
        <td>
          Defines the namespace in which the service account has to be managed
        </td>
        <td>false</td>
      </tr><tr>
        <td><b>annotations</b></td>
        <td>map[string]string</td>
        <td>
          Defines the annotations of the service account
        </td>
        <td>false</td>
      </tr></tbody>
</table>

### WorkloadIdentity.spec.azure

WorkloadIdentityAzure defines the Azure policies

<table>
    <thead>
        <tr>
            <th>Name</th>
            <th>Type</th>
            <th>Description</th>
            <th>Required</th>
        </tr>
    </thead>
    <tbody><tr>
        <td><b>roleDefinitions</b></td>
        <td>[]object</td>
        <td>
          RoleDefinitions is a list of role definitions
        </td>
        <td>false</td>
      </tr><tr>
        <td><b>roleAssignments</b></td>
        <td>map[string]RoleAssignment</td>
        <td>
          Defines the role assignments of the WorkloadIdentity
        </td>
        <td>false</td>
      </tr><tr>
        <td><b>identity</b></td>
        <td>object</td>
        <td>
          Defines the Azure spec identity of workload identity
        </td>
        <td>false</td>
      </tr><tr>
        <td><b>identityBinding</b></td>
        <td>object</td>
        <td>
          Defines the Azure's identiy binding
        </td>
        <td>false</td>
      </tr></tbody>
</table>

### WorkloadIdentity.spec.azure.roleDefinitions

RoleDefinitions defines Azure's role

<table>
    <thead>
        <tr>
            <th>Name</th>
            <th>Type</th>
            <th>Description</th>
            <th>Required</th>
        </tr>
    </thead>
    <tbody><tr>
        <td><b>id</b></td>
        <td>string</td>
        <td>
          Defines the ID of the role. This will be used to generate internal UUID for the role.
        </td>
        <td>true</td>
      </tr><tr>
        <td><b>roleName</b></td>
        <td>string</td>
        <td>
          Defines the name of the roleDefintion
        </td>
        <td>true</td>
      </tr><tr>
        <td><b>roleType</b></td>
        <td>string</td>
        <td>
          Defines the type of the role definition
        </td>
        <td>false</td>
      </tr><tr>
        <td><b>description</b></td>
        <td>string</td>
        <td>
          Defines the description of the role definition
        </td>
        <td>false</td>
      </tr><tr>
        <td><b>assignableScopes</b></td>
        <td>string</td>
        <td>
          Defines the list of assignable scopes
        </td>
        <td>false</td>
      </tr><tr>
        <td><b>permissions</b></td>
        <td>[]object</td>
        <td>
          Defines the permissions for the role definition
        </td>
        <td>true</td>
      </tr></tbody>
</table>

### WorkloadIdentity.spec.azure.roleAssignment

RoleAssignment defines Azure's role assignment

<table>
    <thead>
        <tr>
            <th>Name</th>
            <th>Type</th>
            <th>Description</th>
            <th>Required</th>
        </tr>
    </thead>
    <tbody><tr>
        <td><b>role</b></td>
        <td>string</td>
        <td>
          Defines role of the role assignment
        </td>
        <td>true</td>
      </tr><tr>
        <td><b>scope</b></td>
        <td>string</td>
        <td>
          Defines scope of the role assignment
        </td>
        <td>false</td>
      </tr></tbody>
</table>

### WorkloadIdentity.spec.azure.identity

RoleAssignment defines Azure's identity for workload identity

<table>
    <thead>
        <tr>
            <th>Name</th>
            <th>Type</th>
            <th>Description</th>
            <th>Required</th>
        </tr>
    </thead>
    <tbody><tr>
        <td><b>apiVersion</b></td>
        <td>string</td>
        <td>
          API version of identity
        </td>
        <td>false</td>
      </tr><tr>
        <td><b>kind</b></td>
        <td>string</td>
        <td>
          Defines kind of the identity
        </td>
        <td>false</td>
      </tr><tr>
        <td><b>metadata</b></td>
        <td>object</td>
        <td>
          Defines metadata of identity
        </td>
        <td>false</td>
      </tr><tr>
        <td><b>spec</b></td>
        <td>object</td>
        <td>
          Defines spec of the identity
        <td>false</td>
      </tr></tbody>
</table>

### WorkloadIdentity.spec.azure.identityBinding

RoleAssignment defines Azure's identity binding

<table>
    <thead>
        <tr>
            <th>Name</th>
            <th>Type</th>
            <th>Description</th>
            <th>Required</th>
        </tr>
    </thead>
    <tbody><tr>
        <td><b>apiVersion</b></td>
        <td>string</td>
        <td>
          API version of identity binding
        </td>
        <td>false</td>
      </tr><tr>
        <td><b>kind</b></td>
        <td>string</td>
        <td>
          Defines kind of the identity binding
        </td>
        <td>false</td>
      </tr><tr>
        <td><b>metadata</b></td>
        <td>object</td>
        <td>
          Defines metadata of identity binding
        </td>
        <td>false</td>
      </tr><tr>
        <td><b>spec</b></td>
        <td>object</td>
        <td>
          Defines spec of the identity binding
        <td>false</td>
      </tr></tbody>
</table>