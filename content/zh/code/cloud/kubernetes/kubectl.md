
---
title: "kubectl"
---

## install kubectl

```shell
# kubernetes-cli 
brew install kubectx kube-ps1

# add to ~/.zshrc
source <(kubectl completion zsh)
source "/usr/local/opt/kube-ps1/share/kube-ps1.sh"
PS1='$(kube_ps1)'$PS1
```

## info

```shell
# get all nodePort
kubectl get svc -owide --all-namespaces | grep NodePort | awk '{print $6}' | cut -d: -f2 | cut -d/ -f1
```