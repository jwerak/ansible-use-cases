# Image Builder setup

Setting up of RHEL Image Builder on top of OCP Virtualization.

Quick use case:

- Give AAP access to OCP
- Setup RHEL VM with Image Builder
- Configure Image Builder with blueprint

## Prerequisites

- OCP with Virtualization enabled
- AAP Operator installed

## Setup

### Deploy AAP

```yaml
apiVersion: aap.ansible.com/v1alpha1
kind: AnsibleAutomationPlatform
metadata:
  name: inra-aap
  namespace: aap
spec:
  route_tls_termination_mechanism: Edge
  no_log: true
  redis_mode: standalone
  api:
    log_level: INFO
    replicas: 1
  database:
    postgres_data_volume_init: false
  ingress_type: Route
```

### Configure AAP

#### Credentials

**Obtain Hub Credentials**: Login to Ansible automation hub at https://console.redhat.com/ansible/automation-hub. Navigate to "Automation Hub" -> "Connect to Hub" and click "Load Token" under "Offline Token" to generate a token. Copy down the token, server URL, and SSO URL.

**Add Credentials**: In the Ansible Automation Platform web console, navigate to "Resources" -> "Credentials" and click "Add." Select "Ansible Galaxy/Automation Hub API Token" as the "Credentials Type". Enter the previously obtained token, server URL, and SSO URL.

**Update Organization Settings**: Navigate to "Access" -> "Organizations," select your organization, click "Edit," and go to "Galaxy Credentials." Click the "Search" icon, select the newly created Ansible automation hub credential, and move it to the top of the list to prioritize its use.

**SSH key** Credential.

Create secret for the **remote OpenShift cluster**.

Create Custom **Credentials for KubeVirt Inventory**:

```yaml
---
fields:
  - type: string
    id: kube_api
    label: Kubernetes API Endpoint
  - type: string
    id: kube_token
    label: Kubernetes API Token
    secret: true
required:
  - kube_api
  - kube_token

---
env:
  K8S_AUTH_HOST: "{{ kube_api }}"
  K8S_AUTH_API_KEY: "{{ kube_token }}"
```

#### Workloads

Create the Ansible Automation Platform **Project** from this repository.


## Demo flow

- Inventory pointing to OCP Virt instances
- Project
- JobTemplates:
  - Create VM
  - Setup Image Builder
  - Configure Blueprint on Image Builder

### Configure AAP environment

## Reference

- [AAP & OCP Virtualization blog](https://www.redhat.com/en/blog/ansible-automation-platform-openshift-virtualization-multi-cluster-environment)
