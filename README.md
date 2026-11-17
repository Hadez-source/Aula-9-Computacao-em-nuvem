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

Passo 2
root@controlplane:~$ cat > aula9.yaml <<'EOF'
apiVersion: apps/v1
kind: Deployment
metadata:
  name: web-aula9
  namespace: aula9
spec:
  replicas: 2
  strategy:
    type: RollingUpdate
    rollingUpdate:
      maxSurge: 1
      maxUnavailable: 0
  selector:
    matchLabels: {app: web-aula9}
  template:
    metadata:
      labels: {app: web-aula9}
>     spec:
      containers:
      - name: web
        image: nginx:alpine
        command: ['/bin/sh', '-c']
        args:
        - 'echo "<h1>Versao $VERSAO</h1>" > /usr/share/nginx/html/index.html; exec nginx -g "daemon off;"'
        env:
        - name: VERSAO
          value: '1'
        ports:
        - containerPort: 80
        readinessProbe:
          httpGet: {path: /, port: 80}
          periodSeconds: 3
---
apiVersion: v1
kind: Service
metadata: {name: web-aula9, namespace: aula9}
spec:
  selector: {app: web-aula9}
  ports: [{port: 80, targetPort: 80}]
EOF

Passo 3
root@controlplane:~$ kubectl apply -f aula9.yaml
deployment.apps/web-aula9 created
service/web-aula9 created
root@controlplane:~$ kubectl -n aula9 rollout status deployment/web-aula9
deployment "web-aula9" successfully rolled out
root@controlplane:~$ kubectl -n aula9 get deployment,pods,service
NAME                        READY   UP-TO-DATE   AVAILABLE   AGE
deployment.apps/web-aula9   2/2     2            2           54s

NAME                             READY   STATUS    RESTARTS   AGE
pod/web-aula9-649b495bff-7m2k2   1/1     Running   0          54s
pod/web-aula9-649b495bff-qlmvz   1/1     Running   0          54s

NAME                TYPE        CLUSTER-IP       EXTERNAL-IP   PORT(S)   AGE
service/web-aula9   ClusterIP   10.104.205.116   <none>        80/TCP    54s

Passo 4
root@controlplane:~$ POD=$(kubectl -n aula9 get pods -l app=web-aula9 \
  -o jsonpath='{.items[0].metadata.name}')
root@controlplane:~$ kubectl -n aula9 exec "$POD" -- \
  wget -qO- http://web-aula9
<h1>Versao 1</h1>
root@controlplane:~$ 
