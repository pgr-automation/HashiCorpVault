# HashiCorpVault
# Saving Kubernetes Kubeconfig in HashiCorp Vault

This guide outlines the steps to securely store a Kubernetes `kubeconfig` file in HashiCorp Vault using the KV secrets engine.

## Prerequisites

- A running instance of HashiCorp Vault
- Access to your Kubernetes `kubeconfig` file (commonly located at `~/.kube/config`)

## Steps

### Step 1: Enable the KV Secrets Engine

Ensure the KV (Key-Value) secrets engine is enabled in Vault.

```bash
vault secrets enable -path=kubeconfig kv
```
Option 1: Upload Directly
```bash
vault kv put kubeconfig/my-cluster kubeconfig=@/path/to/kubeconfig
```
Option 2: Base64 Encode and Upload
```bash
base64 /path/to/kubeconfig | vault kv put kubeconfig/my-cluster kubeconfig=-
```
Step 3: Retrieve the kubeconfig from Vault
```bash
vault kv get -field=kubeconfig kubeconfig/my-cluster > ~/.kube/config
```
Retrieve and Decode (if Base64 Encoded)
```bash
vault kv get -field=kubeconfig kubeconfig/my-cluster | base64 -d > ~/.kube/config
```
Step 4: Set Permissions (Optional)

To limit access to the kubeconfig file, create a policy in Vault that allows only specific users or applications to access this secret path.
Example Policy

Create a policy file kubeconfig-policy.hcl with the following contents:
```hcl
path "kubeconfig/my-cluster" {
  capabilities = ["read"]
}
```

Apply the policy to Vault:
```bash
vault policy write kubeconfig-policy kubeconfig-policy.hcl
```
