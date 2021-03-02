# open-policy-agent

OPA decouples policy decision-making from policy enforcement. 

## Constraint Framework

### Enforcement Points
Enforcement Points are places where constraints can be enforced. 
- [Git Hooks](https://git-scm.com/docs/githooks) 
- [Kubernetes Admission Controllers](https://kubernetes.io/docs/reference/access-authn-authz/admission-controllers/)
- Audit Systems

### Constraint
A `Constraint` is a declaration of the requirements the system are to meet.

### Constraint Template


Gatekeeper is uses a crd named `ConstraintTemplate`, which describes validation logic in the `Rego` scripting language and references the `Constrant` crd to enforces policy.

A constraint is a declaration of the requirements the system are to meet.

apiVersion: constraints.gatekeeper.sh/v1beta1
kind: FooSystemRequiredLabel
metadata:
  name: require-billing-label
spec:
  match:
    namespace: ["expensive"]
  parameters:
    labels: ["billing"]
