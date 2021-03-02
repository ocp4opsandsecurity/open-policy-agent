# open-policy-agent

OPA decouples policy decision-making from policy enforcement. 

## Constraint
A `Constraint` crd is a declaration of the requirements the system are to meet.

### Example 
Require the system to enfore that all `Namespaces` have a label.
```yaml
apiVersion: constraints.gatekeeper.sh/v1beta1
kind: K8sUniqueLabel
metadata:
  name: ns-gk-label-unique
spec:
  match:
    kinds:
      - apiGroups: [""]
        kinds: ["Namespace"]
  parameters:
    label: gatekeeper
```

## Constraint Template
A `ConstraintTemplate` crd is the declaration of new constraints along with input parameters and the enforcement logic written `Rego` scripting language.

### Example
Declare the `Constraint` named K8sUniqueLabel with the logic to enforce the precence of labels for a given object/resource. 
```yaml
apiVersion: templates.gatekeeper.sh/v1beta1
kind: ConstraintTemplate
metadata:
  name: k8srequiredlabels
spec:
  crd:
    spec:
      names:
        kind: K8sRequiredLabels
      validation:
        # Schema for the `parameters` field
        openAPIV3Schema:
          properties:
            labels:
              type: array
              items: string
  targets:
    - target: admission.k8s.gatekeeper.sh
      rego: |
        package k8srequiredlabels

        violation[{"msg": msg, "details": {"missing_labels": missing}}] {
          provided := {label | input.review.object.metadata.labels[label]}
          required := {label | label := input.parameters.labels[_]}
          missing := required - provided
          count(missing) > 0
          msg := sprintf("you must provide labels: %v", [missing])
        }
```

## Enforcement Points
`Enforcement Points` are places where constraints can be enforced. 
- [Git Hooks](https://git-scm.com/docs/githooks) 
- [Kubernetes Admission Controllers](https://kubernetes.io/docs/reference/access-authn-authz/admission-controllers/)
- Audit Systems


## Gatekeeper
`Gatekeeper` is uses a crd named `ConstraintTemplate`, which describes validation logic in the `Rego` scripting language and references the `Constrant` crd to enforces policy.

