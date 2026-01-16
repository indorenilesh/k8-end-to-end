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

11. Update the apt package index and install packages needed to use the Kubernetes https certificate configuration:

    apt-get update && apt-get install -y apt-transport-https ca-certificates curl

12. Download the GPG key, as it is used to verify Kubernetes packages from the Kubernetes package repository, and store it in the kubernetes-apt-keyring.gpg file in the /etc/apt/keyrings/ directory.

        curl -fsSL https://pkgs.k8s.io/core:/stable:/v1.33/deb/Release.key | sudo gpg --dearmor -o /etc/apt/keyrings/kubernetes-apt-keyring.gpg
    
***Note: The command above must be run as a single line. Copy it to Notepad first to ensure it's in a single line before running it.***

13. Adding Kubernetes Repository to System's Package Sources	
This command adds the Kubernetes repository to the system's list of package sources by appending the repository information to the file

***Note: Carefully copy and paste the next command. You can paste it into Notepad to make sure it is a single line command.***

        echo 'deb [signed-by=/etc/apt/keyrings/kubernetes-apt-keyring.gpg] https://pkgs.k8s.io/core:/stable:/v1.33/deb/ /' | sudo tee /etc/apt/sources.list.d/kubernetes.list

14. Install kubelet, kubeadm, kubectl packages.

        apt-get update && apt-get install -y kubelet kubeadm kubectl

15. Holding Kubernetes Packages to Prevent Automatic Changes

        apt-mark hold kubelet kubeadm kubectl

16. Enable the kubelet service.

        systemctl enable kubelet 

***Note: We have successfully performed these commands on the Master node. Now, repeat all the steps on both Worker nodes.***
---

### K8 Master server installation steps

---

### K8 Worker server installation steps

