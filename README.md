# eks_template
EKS Template for FFP

# Permissions and roles for istio ( https://github.com/rootsongjc/kubernetes-handbook/raw/master/manifests/istio/istio-rbac-beta.yaml )
# To debug: start the cluster with -vmodule=rbac,3 to enable verbose logging on RBAC DENY
# Also helps to enable logging on apiserver 'wrap' to see the URLs.
# Each RBAC deny needs to be mapped into a rule for the role.
# If using minikube, start with '--extra-config=apiserver.Authorization.Mode=RBAC'
#
# NOTE: If deploying istio to a namespace other than 'default' then change the
# ClusterRoleBinding namspace target appropriately.
#========================================
# ClusterRole
# - istio-manager
# - istio-ca
# - istio-sidecar
#========================================
# ServiceAccount
# - istio-manager-service-account
# - istio-ca-service-account
# - istio-ingress-service-account
#========================================
---
kind: ClusterRole
apiVersion: rbac.authorization.k8s.io/v1beta1
metadata:
  name: istio-manager
rules:
- apiGroups: ["istio.io"]
  resources: ["istioconfigs", "istioconfigs.istio.io"]
  verbs: ["*"]
- apiGroups: ["extensions"]
  resources: ["thirdpartyresources", "thirdpartyresources.extensions", "ingresses", "ingresses/status"]
  verbs: ["*"]
- apiGroups: [""]
  resources: ["configmaps", "endpoints", "pods", "services"]
  verbs: ["*"]
- apiGroups: [""]
  resources: ["namespaces"]
  verbs: ["get", "list"]
- apiGroups: [""]
  resources: ["secrets"]
  verbs: ["create", "get", "watch", "list", "update"]
- apiGroups: [""]
  resources: ["serviceaccounts"]
  verbs: ["watch", "list"]
---
kind: ClusterRoleBinding
apiVersion: rbac.authorization.k8s.io/v1beta1
metadata:
  name: istio-manager-cluster-role-binding
subjects:
- kind: ServiceAccount
  name: istio-manager-service-account
  namespace: default
roleRef:
  kind: ClusterRole
  name: istio-manager
  apiGroup: rbac.authorization.k8s.io
