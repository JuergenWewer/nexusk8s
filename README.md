kubectl create namespace devops-tools

kubectl create -f deployment.yaml

kubectl get po -n devops-tools

kubectl create -f service.yaml

kubectl describe service nexus-service -n devops-tools

access nexus:

http://<cluster IP>:32000

The default username and password will be admin & admin123