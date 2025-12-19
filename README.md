# Custom kubeplugins
---
### Basic plugin for k8s for collecting statistics from real k8s cluster
---
How to install
* create ```vim /usr/local/bin/kubectl-kubeplugin```
* paste there bash code or just copy it from repository **scripts/kubeplugin**
* make it executable: ```chmod +x /usr/local/bin/kubectl-kubeplugin```
* run it ```kubectl kubeplugin```
* follow how to use bellow ->
---
How to use!
```bash
kubectl stats <namespace|all> <resource>

  Resources:
  pods     Show CPU and memory usage for pods
  nodes    Show CPU and memory usage for nodes

  Examples:
  kubectl kubeplugin all pod
  kubectl kubeplugin kube-system pod
  kubectl kubeplugin all nodes
```
examples + output:
```
-> kubectl kubeplugin all pod

Resource,Namespace,Name,CPU,Memory
pod,argocd,argocd-application-controller-0,2m,91Mi
pod,argocd,argocd-applicationset-controller-67cb8b6969-z75tv,1m,22Mi
pod,argocd,argocd-dex-server-7968ff6b6b-6mbvl,0m,19Mi
pod,argocd,argocd-notifications-controller-6cc58b44-r9s9k,1m,20Mi
pod,argocd,argocd-redis-575cc847f6-tpjvl,4m,6Mi
pod,argocd,argocd-repo-server-5b4c7dbf54-mznmj,1m,71Mi
pod,argocd,argocd-server-b887cdfb5-58lwr,1m,25Mi
pod,demo,ambassador-64d97bcfc9-pcsg5,8m,95Mi
pod,demo,cache-7995dc47c8-7v26t,2m,4Mi
pod,demo,db-57657d957c-r664l,4m,429Mi
pod,demo,demo-api-6cf9bb95-d4kzd,1m,4Mi
pod,demo,demo-ascii-7446d8b8b9-kp6bz,1m,4Mi
pod,demo,demo-data-64f785b878-g26n6,1m,4Mi
pod,demo,demo-front-64b97cc6cf-dsncf,1m,4Mi
pod,demo,demo-img-54868756dd-s92zw,1m,4Mi
pod,demo,demo-nats-0,1m,11Mi
pod,demo,demo-nats-box-58f884cff9-ndzsk,1m,0Mi
pod,kube-system,coredns-ccb96694c-rprst,2m,15Mi
pod,kube-system,local-path-provisioner-5cf85fd84d-jl6dd,1m,7Mi
pod,kube-system,metrics-server-5985cbc9d7-8cm9m,3m,22Mi
pod,kube-system,svclb-traefik-66025d0f-rwwt2,0m,2Mi
pod,kube-system,traefik-5d45fc8cc9-tpsrj,1m,32Mi
pod,metallb-system,controller-58859b4d4f-rnqzm,1m,22Mi
pod,metallb-system,speaker-srgnc,2m,17Mi
```
```
-> kubectl kubeplugin all nodes

Node, CPU, RAM, Memory
k3d-argo-server-0,0%,2330Mi,29%
```

---
Just a CODE:
```bash
#!/bin/bash

#!/usr/bin/env bash
set -euo pipefail

if [[ $# -eq 0 ]]; then
cat <<EOF
Usage:
  kubectl stats <namespace|all> <resource>

  Resources:
  pods     Show CPU and memory usage for pods
  nodes    Show CPU and memory usage for nodes

  Examples:
  kubectl kubeplugin all pod
  kubectl kubeplugin kube-system pod
  kubectl kubeplugin all nodes
EOF
exit 0
fi

NAMESPACE="${1:-all}"
RESOURCE_TYPE="${2:-pods}"
if [[ "$RESOURCE_TYPE" == "pod" ]]; then
        if [[ "$NAMESPACE" == "all" ]]; then
                echo "Resource,Namespace,Name,CPU,Memory"
                kubectl top pods --all-namespaces --no-headers |
                while read -r ns name cpu mem; do
                echo "$RESOURCE_TYPE,${ns},${name},${cpu},${mem}"
                done
        else
                echo "Resource,Namespace,Name,CPU,Memory"
                kubectl top pods -n "$NAMESPACE" --no-headers |
                while read -r name cpu mem; do
                echo "$RESOURCE_TYPE,${NAMESPACE},${name},${cpu},${mem}"
                done
        fi
                elif [[ "$RESOURCE_TYPE" == "nodes" ]]; then
                echo "Node, CPU, RAM, Memory"
                kubectl top nodes --no-headers |
                while read -r name _ cpu ram mem _; do
                echo "${name},${cpu},${ram},${mem}"
        done
else
echo "Unsupported resource type: $RESOURCE_TYPE" >&2
exit 1
fi
done
```
---

# The END
