---
layout: default
title: Clusters
nav_exclude: true
---
```
{% for cluster in site.data.clusters %}
module "dcos-{{ cluster.name }}" {
  source = "dcos-terraform/dcos/aws"

  subnet_range = "172.16.0.0/16"

  dcos_instance_os    = "centos_7.5"
  cluster_name        = "dcos-days-{{ cluster.name }}"
  ssh_public_key_file = "~/.ssh/id_dcosdays.pub"
  admin_ips           = ["0.0.0.0/0"]

  num_masters        = "1"
  num_private_agents = "4"
  num_public_agents  = "1"

  bootstrap_instance_type = "t2.medium"
  public_agents_instance_type = "m5.xlarge"
  private_agents_instance_type = "m5.xlarge"
  masters_instance_type = "m5.xlarge"
  dcos_version = "1.12.3"

  dcos_variant = "ee"
  dcos_license_key_contents = "${file("license.txt")}"

  tags = {workshop = "USAF"} 

  public_agents_additional_ports = ["6090", "6443", "7443", "3000", "9090", "9093", "9091", "30443"]

  dcos_superuser_username = "{{ cluster.username }}"

  dcos_superuser_password_hash = "{{ cluster.password  }}"
}

output "masters-ips-{{ cluster.name }}" {
  value = "${module.dcos-{{ cluster.name }}.masters-ips}"
}

output "cluster-address-{{ cluster.name }}" {
  value = "${module.dcos-{{ cluster.name }}.masters-loadbalancer}"
}

output "public-agents-loadbalancer-{{ cluster.name }}" {
  value = "${module.dcos-{{ cluster.name }}.public-agents-loadbalancer}"
}
{% endfor %}
```
