# Setup K8 cluster

## I am using AWS cloud and ubuntu ami for servers.

### Create basic networking like vpc, subnet, internet gateway, nat gateway, route tables, security groups.

Use below cloudformation template file to create above resources

    cf-vpc-3pub-3pri.yml

### Create 3 ec2 instances and use ubuntu ami.

Use below cloudformation template file to create 3 ec2 instances.

    cf-3-ec2.yml

### Below steps need to perform on Master as well as on Worker node

#### use root user
    sudo su -

#### Configure Kernel Modules to containerd Configuration File

    tee /etc/modules-load.d/containerd.conf <<EOF
    overlay
    br_netfilter
    EOF




### K8 Master server installation steps


### K8 Worker server installation steps

