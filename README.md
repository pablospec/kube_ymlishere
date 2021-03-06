aws:
  region: eu-west-1
  backend_s3:
    bucket: felipeneuwald-kubernetes-cluster
    key: kubernetes-cluster/terraform
    region: eu-west-1
  ssh_key:
    key_name: kubernetes-cluster
    public_key: "ssh-rsa AAAAB3NzaC1yc2EAAAADAQABAAABAQDY4UiC2rYhxwFP9FzHa6kqXn1q4D/OGIWg72sFVg2oyJgHK1u//xaon9qNV0BQRbIeXWgT5WkGQP+4TKxHmPpy43/Py8r+OMWQgx+WuFa1ocPEngQZu52/KPgHfDT234P/qWwefrBW2x2Z4A+EnVkz2ChSXmNRLgm2VEqKNKPJdW06tH+zBUm7YD/60KzDW2RzQTfiZ/wJl031/yrHgyOy1RkiAtmnnphiO10dj3cX3Nzb+raOYjx5Qx5glqnVN4qJJfCUn2OtvR2Ll11XLh7FesLDg5Usd9C6MCDPbQXZ6qO/B5cDKcmW26pxASKFuDeUvZwcSsTLJqsSeHpo/Lbh kubernetes-cluster"
  network:
    vpc_cidr_block: 10.0.0.0/16
    subnet_a:
      cidr_block: 10.0.1.0/24
      availability_zone: eu-west-1a
    subnet_b:
      cidr_block: 10.0.2.0/24
      availability_zone: eu-west-1b
    subnet_c:
      cidr_block: 10.0.3.0/24
      availability_zone: eu-west-1c
  nodes:
    etcd:
      instance_ami: ami-02df9ea15c1778c9c
      instance_type: t3a.small
      root_block_device_volume_size: 10
      monitoring: "false"
      ebs_optimized: "false"
    control_plane:
      instance_ami: ami-02df9ea15c1778c9c
      instance_type: t3a.small
      root_block_device_volume_size: 10
      monitoring: "false"
      ebs_optimized: "false"
    worker:
      instance_ami: ami-02df9ea15c1778c9c
      instance_type: t3a.small
      root_block_device_volume_size: 10
      nodes_per_az: 1
      monitoring: "false"
      ebs_optimized: "false"
  load_balancer:
    kube_apiserver:
      idle_timeout: 60
      connection_draining: "true"
      connection_draining_timeout: 300
      instance_port: 6443
      lb_port: 6443
      health_check:
        healthy_threshold: 6
        unhealthy_threshold: 3
        timeout: 2
        interval: 10
ansible:
  config:
    remote_user: ubuntu
    private_key_file: ~/.ssh/kubernetes-cluster
    host_key_checking: False
    interpreter_python: auto
    inventory: inventories/ec2.py
  base:
    packages:
      - aptitude
      - apt-transport-https
      - ca-certificates
      - curl
      - software-properties-common
  docker:
    repository:
      key: https://download.docker.com/linux/ubuntu/gpg
      source: "deb [arch=amd64] https://download.docker.com/linux/ubuntu bionic stable"
    packages:
      - package: docker-ce-cli
        version: 5:19.03.4~3-0~ubuntu-bionic
      - package: docker-ce
        version: 5:19.03.4~3-0~ubuntu-bionic
  kubernetes:
    repository:
      key: https://packages.cloud.google.com/apt/doc/apt-key.gpg
      source: "deb https://apt.kubernetes.io/ kubernetes-xenial main"
    packages:
      - kubectl
      - kubelet
      - kubeadm
...
