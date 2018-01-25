> The TL;DR  of https://docs.openshift.com/container-platform/3.7/welcome/index.html 

##  Checklist
- check time on each machine
- check ssh access from bastion to all nodes
- check ports 
- check storage
- check subscriptions
- check selinux
- check dns  wildcard
- check RHEL version


## Host-prep
https://docs.openshift.com/container-platform/3.7/install_config/install/host_preparation.html
```sh
subscription-manager register --username=<user_name> --password=<password>
subscription-manager refresh
subscription-manager list --available --matches '*OpenShift*'
subscription-manager attach --pool=<pool_id>
subscription-manager repos --disable="*"

subscription-manager repos \
    --enable="rhel-7-server-rpms" \
    --enable="rhel-7-server-extras-rpms" \
    --enable="rhel-7-server-ose-3.7-rpms" \
    --enable="rhel-7-fast-datapath-rpms"
 
 yum install wget git net-tools bind-utils iptables-services bridge-utils bash-completion kexec-tools sos psacct
 
 yum update
 
 yum install atomic
 yum install atomic-openshift-utils
 yum install docker-1.12.6
 
cat <<EOF > /etc/sysconfig/docker-storage-setup
DEVS=/dev/vdc
VG=docker-vg
EOF

docker-storage-setup           
systemctl enable docker
systemctl restart docker


## Install
```sh
atomic-openshift-installer install
```
