> The TL;DR  of https://docs.openshift.com/container-platform/3.7/welcome/index.html 

##  Checklist
- check machine config  cpu/mem
- check time on each machine
- check ssh access from bastion to all nodes
- check ports 
- check storage
- check selinux
- check dns  wildcard
- check RHEL version
- check subscriptions
- check proxy config
- check subnet conflicts


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
subscription-manager repos --enable=rh-gluster-3-client-for-rhel-7-server-rpms
yum install glusterfs-fuse -y 
  
 yum install wget git net-tools bind-utils iptables-services bridge-utils bash-completion kexec-tools sos psacct -y

 yum update -y
 
 yum install atomic -y
 yum install atomic-openshift-utils -y 
 yum install docker-1.12.6 -y
 
cat <<EOF > /etc/sysconfig/docker-storage-setup
DEVS=/dev/vdc
VG=docker-vg
EOF

docker-storage-setup           
systemctl enable docker
systemctl restart docker
```
### Proxy details in 
```sh
cat /etc/environment
HTTP_PROXY="http://myproxy.server.com:8080/"
HTTPS_PROXY="http://myproxy.server.com:8080/"
NO_PROXY="localhost,127.0.0.1,localaddress,.localdomain.com,master.hostname.example.com,10.1.0.0/16,172.30.0.0/16"
```
## Install

```sh
atomic-openshift-installer install
atomic-openshift-installer -u install
ansible-playbook -i /root/.config/openshift/hosts  /usr/share/ansible/openshift-ansible/playbook/byo/config.yml
```

### Install vars
```sh
openshift_master_default_subdomain=apps.osecloud.com
openshift_docker_insecure_registries=172.30.0.0/16
openshift_disable_check=disk_availability,docker_storage,memory_availability
openshift_no_proxy='.hosts.example.com,some-host.com'
os_sdn_network_plugin_name='redhat/openshift-ovs-multitenant'
```


