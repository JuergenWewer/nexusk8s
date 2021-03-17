kubectl create namespace devops-tools

kubectl create -f deployment.yaml

kubectl get po -n devops-tools

kubectl create -f service.yaml
kubectl create -f service-client.yaml


kubectl describe service nexus-service -n devops-tools

access nexus:

http://<cluster IP>:32000
http://10.211.55.4:32000

The default username and password will be admin & admin123
password im nexus container unter: /nexus-data/admin.password

dann im setup

password: admin123
enable anonym access

in ~/.docker/daemon.json
add:
"insecure-registries":["10.211.55.4:32000"]
on mac, desktop docker restart
on debian:
systemctl daemon-reload
systemctl restart docker
check with docker info:

add repository:

docker host
docker-repo
http: 8132
Allow anonymous docker pull - nicht aktivieren
enable v1

check with:

docker login http://10.211.55.4:32132

kubectl create ...
just for test use the flag --dry-run=client -o yaml

kubectl create -f deployment-w-pvc.yaml --dry-run=client -o yaml
kubectl create -f persistentvolumeclaim.yaml --dry-run=client -o yaml
kubectl create -f persistentvolume.yaml --dry-run=client -o yaml

kubectl create namespace devops-tools
kubectl create -f persistentvolume.yaml
kubectl create -f persistentvolumeclaim.yaml
kubectl create -f deployment-w-pvc.yaml
kubectl create -f service.yaml
kubectl create -f service-client.yaml

cd playbooks
ansible-playbook -i macpro start.yml -v

create docker repository

docker host
docker-repo
http: 8132
Allow anonymous docker pull - nicht aktivieren
enable v1

check with:

docker login http://10.211.55.4:32132
