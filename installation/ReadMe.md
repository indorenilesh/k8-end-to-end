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

3. Load the kernel modules into the running Linux kernel.

        modprobe overlay
        modprobe br_netfilter

4. Update the iptables settings by setting net.bridge.bridge-nf-call-iptables to 1 in your sysctl config file to ensure proper packet processing during filtering and port forwarding.

        tee /etc/sysctl.d/kubernetes.conf<<EOF
        net.bridge.bridge-nf-call-ip6tables = 1
        net.bridge.bridge-nf-call-iptables = 1
        net.ipv4.ip_forward = 1
        EOF
    
5. Applying Kernel Settings Without Reboot

        sysctl --system

6. Adding Docker Repository GPG Key to Trusted Keys, so it allows the system to verify the integrity of the downloaded Docker packages. 

        mkdir -m 0755 -p /etc/apt/keyrings
        curl -fsSL https://download.docker.com/linux/ubuntu/gpg | sudo gpg --dearmor -o /etc/apt/keyrings/docker.gpg

7. Adding a Docker Repository to Ubuntu Package Sources enables the system to download and install Docker packages from the specified repository.

        echo "deb [arch=$(dpkg --print-architecture) signed-by=/etc/apt/keyrings/docker.gpg] \ 
        https://download.docker.com/linux/ubuntu $(lsb_release -cs) stable" | sudo tee /etc/apt/sources.list.d/docker.list > /dev/null

8. Install containerd

        apt-get update && apt-get install -y containerd.io

***If you encounter Malformed entry 1 in the list file /etc/apt/sources.list.d/docker.list (URI) error. Please refer to the troubleshooting section for detailed steps to resolve the error***

9. Configure containerd for Systemd Cgroup Management to enable the use of systemd for managing cgroups in containerd.

    mkdir -p /etc/containerd
    containerd config default>/etc/containerd/config.toml
    sed -e 's/SystemdCgroup = false/SystemdCgroup = true/g' -i /etc/containerd/config.toml

10. Reloading Daemon, Restarting, Enabling, and Checking containerd Service Status

        systemctl daemon-reload
        systemctl restart containerd
        systemctl enable containerd
        systemctl status  containerd

---

### K8 Master server installation steps

---

### K8 Worker server installation steps

