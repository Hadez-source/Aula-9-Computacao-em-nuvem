# Aula-9-Computacao-em-nuvem

Passo 1
root@controlplane:~$ kubectl get nodes
NAME           STATUS   ROLES           AGE   VERSION
controlplane   Ready    control-plane   11d   v1.37.1
node01         Ready    <none>          11d   v1.37.1
root@controlplane:~$ kubectl create namespace aula9
namespace/aula9 created
root@controlplane:~$ kubectl get namespace aula9
NAME    STATUS   AGE
aula9   Active   10s
