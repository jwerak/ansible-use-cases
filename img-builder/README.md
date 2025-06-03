# Image Builder setup

Setting up of RHEL Image Builder on top of OCP Virtualization.

Quick use case:

- Give AAP access to OCP
- Setup RHEL VM with Image Builder
- Configure Image Builder with blueprint

## Prerequisites

- OCP with Virtualization enabled
- AAP Operator installed
- **Obtain Hub Credentials**: Login to Ansible automation hub at <https://console.redhat.com/ansible/automation-hub/token>. Navigate to "Automation Hub" -> "Connect to Hub" and click "Load Token" under "Offline Token" to generate a token. Copy down the token, server URL, and SSO URL.
- **AAP Subscription Manifest**: Go to [subscription allocations](https://access.redhat.com/management/subscription_allocations) and export zip manifest.
Import manifest to AAP.

## Setup

### Deploy AAP

Install AAP Operator via GUI.

Install AAP instance:

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

### Configure AAP - automated

- Copy file ./secrets/aap_secrets.yml.example -> ./secrets/aap_secrets.yml
- Update values
  - `automation_hub_token` from [automation-hub](https://console.redhat.com/ansible/automation-hub/token)
  - `aap_hostname`: ${AAP-URL} (not aap controller)
  - `aap_token` from ${AAP}-URL/access/users/
- Optional: test connection to controller

        curl -H "Authorization: Bearer TOKEN" https://inra-aap-aap.apps.cluster-2kxf6.dynamic.redhatworkshops.io/api/controller/v2/organizations/

- Run setup playbook
  - `ansible-navigator run setup.yml`

### Configure AAP - manually

#### Credentials

**Add Credentials**: In the Ansible Automation Platform web console, navigate to "Resources" -> "Credentials" and click "Add." Select "Ansible Galaxy/Automation Hub API Token" as the "Credentials Type". Enter the previously obtained token, server URL, and SSO URL.

**Update Organization Settings**: Navigate to "Access Management" -> "Organizations," select your organization, click "Edit," and go to "Galaxy Credentials." Click the "Search" icon, select the newly created Ansible automation hub credential, and move it to the top of the list to prioritize its use.

**SSH key** Credential.

Create secret for the **remote OpenShift cluster**.

Add Execution Environment `quay.io/jveverka/ansible-official-ee-aap`.

Create Custom **Credentials for KubeVirt Inventory** (old):

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

## TODO

- automate OCP Service account creation
- automate AAP subscription and instance creation
- update flow to create project and secrets, and create setup JobTemplate to set everything up

## Reference

- [AAP & OCP Virtualization blog](https://www.redhat.com/en/blog/ansible-automation-platform-openshift-virtualization-multi-cluster-environment)
- [automation controller roles](https://github.com/redhat-cop/infra.aap_configuration)
  - [automation-hub link](https://console.redhat.com/ansible/automation-hub/repo/validated/infra/aap_configuration/)
