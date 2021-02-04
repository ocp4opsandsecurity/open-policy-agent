# open-policy-agent

## Conftest
[Conftest](https://github.com/open-policy-agent/conftest) is a CLI tool that 
execute OPA policies against a YAML/JSON dataset. 

[Installation Instructions](https://www.conftest.dev/install/)


## Gatekeeper
[Gatekeeper](https://github.com/open-policy-agent/gatekeeper) is a set of Pods 
that leverage the Admission Controller webhook to natively enforce policy.

### Assumption
- Access to the `oc command`
- Access to a user with cluster-admin permissions
- Access to OpenShift Container Platform 4.6 deployment
- Access to an active OpenShift Container Platform 4.6 subscription

### Installation
Install `gatekeeper` using the following command:
```bash
oc apply -f https://raw.githubusercontent.com/open-policy-agent/gatekeeper/release-3.2/deploy/gatekeeper.yaml
```
