
---
title: ".zshrc"
---

## mac

```shell
alias docker="podman"
alias dk="docker"
alias dki="dk image"
alias dke="dk exec -it"
alias dkr="dk run --rm=true"
alias dkl="dk logs -f"
alias dkv="dk volume"
alias dkn="dk network"
alias dkc="dk container"
alias dkic="dk inspect container"
alias dkii="dk inspect image"
alias dkm="dk machine"
alias docker-compose="podman-compose"
alias dkp="docker-compose"
alias dkpe="docker-compose exec"

alias k='kubectl'
alias kx='kubectx'
alias kn='kubens'
alias km='minikube'
alias ke='kubectl exec -it'
alias kl='kubectl logs -f --tail=4000'
alias kb='kubectl describe'
alias kaf="kubectl apply -f"
alias kg='kubectl get -o wide'
alias kgp='kubectl get pod -o wide'
alias kgd='kubectl get deploy -o wide'
alias kgs='kubectl get svc -o wide'
alias kgn='kubectl get no -o wide --show-labels'
alias kc='kubectl create'
alias kcn='kubectl create namespace'
alias kd='kubectl delete'
alias kdf='kubectl delete -f'
alias kdp='kubectl delete po'
alias kdd='kubectl delete deploy'
alias kds='kubectl delete svc'
alias kp='kubectl port-forward --address 0.0.0.0'
alias kem='kubectl edit cm'

alias mvncc="mvn clean"
alias mvnc="mvn compile -Dmaven.test.skip=true"
alias mvnp="mvn clean compile package -Dmaven.test.skip=true"
alias mvni="mvn  install -Dmaven.test.skip=true"
alias mvnd="mvn clean compile deploy -Dmaven.test.skip=true"
alias fmtrs='find . -type f -name "*.rs" | egrep -v "target/\w+/build" | xargs rustfmt'
alias flush_touchbar='sudo pkill TouchBarServer'
```
