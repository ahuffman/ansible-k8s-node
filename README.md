# ahuffman.k8s-node

An ansible role to configure kubernetes nodes/minions on Red Hat Enterpise Linux based hosts.

This role can deploy either a SSL Secured kubernetes node (kubelet) or an insecured kubernetes node.  See the example playbooks for the minimum required variables to deploy cluster nodes in either an insecure or secure configuration.

## Requirements

If connecting to a kubernetes cluster, you may want to consider making use of the [`k8s-master`](https://galaxy.ansible.com/ahuffman/k8s-master/) role when deploying your master, especially if you wish to make use of the secure configuration.

## Role Variables  
###Defaults:   
Found in [`defaults/main.yml`](defaults/main.yml).   
#### k8s-node Role settings:
`k8s_secure_node`: false - Whether or not to configure the node to connect to the kubernetes master over secure channels.  Deployment of your kubernetes master via the [`k8s-master`](https://galaxy.ansible.com/ahuffman/k8s-master/) role is required for this to be able to generate the node certificates properly.   

#### Docker engine settings:   
`docker_options`: --selinux-enabled - You can pass additional options here if you wish.  See `man docker` for all available options.   
`docker_cert_path`: /etc/docker - Default path for docker engine certificates.   
`docker_add_registry`: '' - String of docker registries separated by spaces.   
`docker_insecure_registry`: '' - String of insecure docker registries separated by spaces.   
`docker_block_registry`: '' - String of docker registries you would like to block from image pulling.   
`docker_tmpdir`: /var/lib/docker/tmp - Temporary docker storage path.   

#### Flanneld Network Settings/Etcd Client Settings:   
`etcd_server_url`: http://kubernetes.local - URL (minus the port number) to your etcd server.  If using the [`k8s-master`](https://galaxy.ansible.com/ahuffman/k8s-master/) role, then this should be replaced by the URL to your deployed master server.   
`etcd_port`: 2379 - The port on which to communicate with your etcd server.   
`etcd_key`: /kube01/network - The key in etcd to obtain flanneld's network configuration from.  This should match the key you deployed with the [`k8s-master`](https://galaxy.ansible.com/ahuffman/k8s-master/) role.   

#### Kubernetes node settings:   
`k8s_master_hostname`: '' - This should be set to the short (non-FQDN) hostname of your kubernetes master server so that this role can grab the appropriate CA certificates from the Ansible control server.  It is also used to configure the kubelet and other components of your node.   
`k8s_master_insecure_port`: 8080 - The port to communicate insecurely with your kubernetes master on.   
`k8s_master_secure_port`: 6443 -  The port to communicate securely with your kubernetes master on.   
`k8s_allow_privileged`: false - Whether or not to allow privileged containers (i.e. containers that can obtain root level access to your node systems.)   
`k8s_log_level`: 0 - Verbosity of your kubernetes node's logging.   
`k8s_logtostderr`: true - Whether or not your node will log to standard error.   

#### Secured kubernetes node settings:   
`k8s_node_cert_path`: /etc/kubernetes/certs - The path where your secured kubernetes node certificates will be stored.   
`k8s_apiserver_ca_key_filename`: ca.key - The destination filename of the ca.key retrieved off of the Ansible control server on the node during certificate generation.   
`k8s_apiserver_ca_cert_filename`: ca.crt - The destination filename of the ca.crt retrieved off of the Ansible control server on the node during certificate generation.   
`k8s_node_server_key_filename`: server.key - The filename of the generated server private key file on the node.   
`k8s_node_server_csr_filename`: server.csr - The filename of the generated certificate signing request on the node.   
`k8s_node_server_cert_filename`: server.cert - The filename of the generated certificate on the node.   
`k8s_proxy_kubeconfig_filename`: proxy.kubeconfig - The filename of the kube-proxy kubeconfig file required for secure communication with the kubernetes master.   
`k8s_kubelet_kubeconfig_filename`: kubelet.kubeconfig - The filename of the kubelet kubeconfig file required for secure communication with the kubernetes master.   

#### Optional Settings:   
`k8s_cluster_domain`: '' - If deploying the skydns service to your kubernetes cluster, you will want to set the domain as a string here.   
`kubelet_hostname_override`:  '' - By default the kubelet uses the FQDN of the kubelet when registering itself with the kubernetes master.  If you wish to have a particular node be represented by a more meaningful name, you may want to override the default with this option.   
`k8s_cluster_dns_ip`: '' - If deploying the skydns service to your kubernetes cluster, you will want to specify the service's IP address for your kubelets to work properly in that configuration.   
`kubelet_additional_args`: '' - Any additional kubelet options you'd like to pass to the configuration.  See `man kubelet` for all available options.   
`kube_proxy_additional_args`: '' - Any additional kube-proxy options you'd like to pass to the configuration.  See `man kube-proxy` for all available options.   
###Variables:  
Found in [`vars/main.yml`](vars/main.yml).   

The required packages for configuring your kubernetes nodes will be installed using the variable `k8s_node_packages`.   

      k8s_node_packages:
        - flannel
        - docker
        - kubernetes-node
        - openssl - Required for certificate generation   
        - yum-utils - Required for determining docker's currently installed version to account for a compatibility option in older versions   


Due to the docker daemon option switching from `-d` to `daemon` this variable was required to compare the current installed version against a list of previous versions that utilized the `-d` option.  This will insert the proper flanneld docker systemd drop-in option into the configuration based on the docker version.   

      docker_ver_check:
        - 0.11.1
        - 1.1.2
        - 1.2.0
        - 1.3.2
        - 1.4.1
        - 1.5.0
        - 1.6.0
        - 1.6.2
        - 1.7.1
        - 1.8.2


## Example Playbook   
### Insecured kubernetes node, where 192.168.122.20 is the IP address of the etcd server (and in this case also the kubernetes master.)   

      - hosts: kubernetes_nodes
        vars:
          k8s_master_hostname: kubmst01
          etcd_server_url: http://192.168.122.20
        roles:
          - k8s-node   

### Secured kubernetes node, where 192.168.122.20 is the IP address of the etcd server (and in this case also the kubernetes master.)   

      - hosts: kubernetes_nodes
        vars:
          k8s_secure_node: true
          k8s_master_hostname: kubmst01
          etcd_server_url: http://192.168.122.20
        roles:
          - k8s-node   

### For examples utilizing both the `k8s-master` role in conjunction with the `k8s-node` role, please see the example playbooks in the [`k8s-master`](https://galaxy.ansible.com/ahuffman/k8s-master/) role.   


## License   

[MIT](LICENSE)

## Author Information   

[Andrew J. Huffman](https://github.com/ahuffman)
