k8s-node
=========

An ansible role to configure kubernetes nodes/minions on Red Hat Enterpise Linux based hosts.

Requirements
------------

N/A

Role Variables
--------------

###Defaults:
####docker settings
* docker_options: --selinux-enabled
* docker_cert_path: /etc/docker
* docker_add_registry: 'registry.access.redhat.com' #string separated by spaces to add more docker registries
* docker_insecure_registry: '' #string separated by spaces to add more docker insecure registries
* docker_block_registry: '' #string separated by spaces to block more docker registries
* docker_tmpdir: /var/lib/docker/tmp

####flannel network settings
* etcd_server_url: http://kubernetes.local
* etcd_port: 2379
* etcd_key: /kube01/network

####kubernetes node settings
* k8s_master_url: http://kubernetes.local
* k8s_master_port: 8080
* k8s_allow_privileged: false
* k8s_log_level: 0
* k8s_logtostderr: true
* kubelet_address: 0.0.0.0

####Optional
* k8s_cluster_domain: '' #ex: kubernetes.local
* kubelet_hostname_override: ''
* k8s_cluster_dns_ip: '' #use if you have deployed skydns
* kubelet_additional_args: '' #use if you want to pass additional kubelet switches
* k8s_kube_proxy_additional_args: '' #use if you want to pass additional kube-proxy switches

###Variables:
####kubernetes packages
* k8s_node_packages:
    - flannel
    - docker
    - kubernetes-node

Dependencies
------------

N/A

Example Playbook
----------------
Bare minimum requirements where 192.168.122.20 is the IP address of the master server.  The defaults take care of the rest.  See k8s-master bare minimum requirements to ensure the configuration lines up with the defaults there.

    - hosts: kubernetes_nodes
      vars:
        k8s_master_url: http://192.168.122.20
        etcd_server_url: http://192.168.122.20
      roles:
         - k8s-node

License
-------

MIT

Author Information
------------------

Andrew J. Huffman
