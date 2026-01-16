# Setup K8 cluster

## I am using AWS cloud and ubuntu ami for servers.

### Create basic networking like vpc, subnet, internet gateway, nat gateway, route tables, security groups.

Use below cloudformation template file to create above resources

    cf-vpc-3pub-3pri.yml

### Create 3 ec2 instances and use ubuntu ami.

Use below cloudformation template file to create 3 ec2 instances.

    cf-3-ec2.yml
---

### Below steps need to perform on Master as well as on Worker node

1. use root user

    sudo su -

2. Configure Kernel Modules to containerd Configuration File

    tee /etc/modules-load.d/containerd.conf <<EOF
    overlay
    br_netfilter
    EOF

#### Load the kernel modules into the running Linux kernel.

    modprobe overlay
    modprobe br_netfilter

#### Update the iptables settings by setting net.bridge.bridge-nf-call-iptables to 1 in your sysctl config file to ensure proper packet processing during filtering and port forwarding.

    tee /etc/sysctl.d/kubernetes.conf<<EOF
    net.bridge.bridge-nf-call-ip6tables = 1
    net.bridge.bridge-nf-call-iptables = 1
    net.ipv4.ip_forward = 1
    EOF
    
#### Applying Kernel Settings Without Reboot

    sysctl --system

#### Adding Docker Repository GPG Key to Trusted Keys, so it allows the system to verify the integrity of the downloaded Docker packages. 

    mkdir -m 0755 -p /etc/apt/keyrings
    curl -fsSL https://download.docker.com/linux/ubuntu/gpg | sudo gpg --dearmor -o /etc/apt/keyrings/docker.gpg

#### Adding a Docker Repository to Ubuntu Package Sources enables the system to download and install Docker packages from the specified repository.

    echo "deb [arch=$(dpkg --print-architecture) signed-by=/etc/apt/keyrings/docker.gpg] \ 
    https://download.docker.com/linux/ubuntu $(lsb_release -cs) stable" | sudo tee /etc/apt/sources.list.d/docker.list > /dev/null

---

### K8 Master server installation steps

---

### K8 Worker server installation steps

