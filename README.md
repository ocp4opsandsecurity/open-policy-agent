# open-policy-agent

OPA decouples policy decision-making from policy enforcement. 

## Constraint
Constraints are then used to communicate that a ConstraintTemplate wants to be enforced by declaration of the requirements the system is to meet.

### Constraint Example 
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

The `match` field defines the scope of objects to which a given constraint will be applied. It supports the following matchers:
- `kinds` accepts a list of objects with apiGroups and kinds fields that list the groups/kinds of objects to which the constraint will apply. If multiple groups/kinds objects are specified, only one match is needed for the resource to be in scope.
- `scope` accepts *, Cluster, or Namespaced which determines if cluster-scoped and/or namesapced-scoped resources are selected. (defaults to *)
- `namespaces` is a list of namespace names. If defined, a constraint will only apply to resources in a listed namespace.
- `excludedNamespaces` is a list of namespace names. If defined, a constraint will only apply to resources not in a listed namespace.
- `labelSelector` is a standard Kubernetes label selector.
- `namespaceSelector` is a standard Kubernetes namespace selector. If defined, make sure to add Namespaces to your configs.config.gatekeeper.sh object to ensure namespaces are synced into OPA. 

## Constraint Template
A `ConstraintTemplate` crd is the declaration of new constraints along with input parameters and the enforcement logic written `Rego` scripting language.

### Constraint Template Example
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
- [Audit Systems](https://open-policy-agent.github.io/gatekeeper/website/docs/audit)


## Gatekeeper
`Gatekeeper` is a Kubernetes admission webhook which enforces policy.

