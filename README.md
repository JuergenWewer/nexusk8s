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
{"insecure-registries":["10.211.55.4:32132"]}

auf debian:
in /etc/docker/daemon.json
{"insecure-registries":["10.0.1.51:32132"]}
on mac, desktop docker restart
on debian:
systemctl daemon-reload
systemctl restart docker
check with docker info:

add repository:

docker (hosted)
docker-repo
http: 8132
Allow anonymous docker pull - nicht aktivieren
enable v1

check with:

docker login http://10.211.55.4:32132
docker login http://10.0.1.51:32132

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

###############################################################################
##         to start: create nexus
###############################################################################
cd playbooks
export KUBECONFIG=/Users/wewer/.kube/master/etc/kubernetes/admin.conf
ansible-playbook -i macpro start.yml -v

create docker repository:
access nexus:

http://<cluster IP>:32000
http://10.211.55.4:32000

ggf:
ssh jwewer@10.0.1.53

The default username and password will be admin & admin123
password im nexus container unter: /nexus-data/admin.password

dann im setup

password: admin123
enable anonym access

create new repository:
docker (hosted)
docker-repo
http: 8132
Allow anonymous docker pull - nicht aktivieren
enable v1

check with:

docker login http://10.211.55.4:32132
#####################################################################################

#####################################################
install nexus on aws ec2 with aws cli
#####################################################

neuen benutzer anlegen in aws

name: nexus
ID: AKIAQY6ILEDCQTLM4JF2
Schlüssel: ZKW9EnhTV3dhvThNMQE0jvoUlHsedRAuhDm2v7UU

dann:

aws configure

see

https://docs.aws.amazon.com/cli/latest/userguide/cli-services-ec2.html

1. create EC2 key pairs:

aws ec2 create-key-pair --key-name NexusJW --query 'KeyMaterial' --region --output text > NexusJW.pem

in aws neue instance auf basis debian 10 gestartet

öffentliche ip:
3.122.106.182

ssh -i "NexusJW.pem" admin@ec2-3-122-106-182.eu-central-1.compute.amazonaws.com
volumes hinzugefügt:
/dev/sdf

auf der instance
lsblk

check ob filesystem auf datenträger vorhanden ist:

sudo file -s /dev/xvdf
wenn nur data angezeigt wird ist keins vorhanden

filesystem erstellen (nur einmalig)
sudo mkfs -t xfs /dev/xvdf

sudo mkdir /data
sudo mount /dev/xvdf /data

install java 8:
https://linuxize.com/post/install-java-on-debian-10/


nexus-3.30.1-01-unix.tar.gz

wget https://sonatype-download.global.ssl.fastly.net/repository/downloads-prod-group/3/nexus-3.30.1-01-unix.tar.gz

sudo useradd -M -d /data/nexus -s /bin/bash -r nexus

sudo tar xzf  nexus-3.30.1-01-unix.tar.gz -C /data/nexus --strip-components=1

sudo /data/nexus/bin/nexus start

password unter: /data/sonatype-work/nexus3/admin.password

new credentials:
admin
admin123jwabcdef

docker login  https://jw-cloud.org:18443

log: /data/sonatype-work/nexus3/log/nexus.log

netstat -altnp

keytool -genkeypair -keystore keystore.jks -storepass password -alias 3.122.106.182 \
-keyalg RSA -keysize 2048 -validity 5000 -keypass password \
-dname 'CN=3.122.106.182, OU=Sonatype, O=Sonatype, L=Unspecified, ST=Unspecified, C=US' \
-ext 'SAN=DNS:3.122.106.182'
Output: keystore.jks

keytool -exportcert -keystore keystore.jks -alias 3.122.106.182 -rfc > example.cert
Output: example.cert

keytool -importkeystore -srckeystore keystore.jks -destkeystore example.p12 -deststoretype PKCS12
Output: example.p12

keytool -list -keystore example.p12 -storetype PKCS12

sudo nano /data/sonatype-work/nexus3/etc/nexus.properties
application-port-ssl=443
${jetty.etc}/jetty-https.xml
ssl.etc=${karaf.data}/etc/ssl

sudo nano /data/sonatype-work/nexus3/etc/jetty/jetty-https.xml
<Set name="/home/admin/example.p12">

sudo /data/nexus/bin/nexus stop
sudo /data/nexus/bin/nexus start

sudo mkdir /data/sonatype-work/nexus3/etc/ssl
sudo cp example.p12 /data/sonatype-work/nexus3/etc/ssl/keystore.jks

test to push a image to docker repo:
curl -v -u admin:admin123jwabcdef --upload-file /Users/wewer/workspace/yuuvisvapi/target/yuuvis-v-api-2.0.0-SNAPSHOT.jar https://jw-cloud.org/repository/maven-snapshots/juergenwewer/optimal/yuuvis-v-api/1.3-SNAPSHOT/yuuvis-v-api-1.3-SNAPSHOT.jar
