<h2 id="a-few-things-you-should-know">
    <strong>1. Install Required Packages</strong>
</h2>
<blockquote>
    apt install qemu-guest-agent<br>
    apt update && apt dist-upgrade
</blockquote>
<h2 id="a-few-things-you-should-know">
    <strong>2. Configure Network Settings</strong>
</h2>

<h3 id="a-few-things-you-should-know">
    <strong>Disable Cloud-init Network Management</strong>
</h3>

<blockquote>
    sudo touch /etc/cloud/cloud-init.disabled<br>
    sudo rm /etc/netplan/50-cloud-init.yaml<br>
    sudo rm -rf /etc/cloud<br>
    sudo reboot<br>
</blockquote>

<h3 id="a-few-things-you-should-know">
    <strong>Example Network Configuration (Static IP)</strong>
</h3>

<blockquote>
    cd /etc/netplan<br>
    vim 00-installer-config.yaml
</blockquote>

<pre>
network:
    version: 2
    ethernets:
        ens18:
            dhcp4: false
            addresses:
                - 10.1.1.100(vm-address)/24
            routes:
                - to: default
                  via: 10.1.1.1(reverse-proxy-address)
            nameservers:
                addresses:
                  - 140.127.74.142(My lab's static ip)
                  - 140.127.40.3(My school's static ip)
</pre>
<blockquote>
    sudo chmod 600 /etc/netplan/00-installer-config.yaml<br>
    sudo netplan generate<br>
    sudo netplan apply
</blockquote>

<h2 id="a-few-things-you-should-know">
    <strong>3. Install and Configure Containerd</strong>
</h2>
<blockquote>
    apt install containerd<br>
    systemctl status containerd<br>
    mkdir /etc/containerd<br>
    containerd config default | tee /etc/containerd/config.toml
</blockquote>
<h3 id="a-few-things-you-should-know">
    <strong>Modify Configuration</strong>
</h3>
<blockquote>
    nano /etc/containerd/config.toml<br>
    SystemdCgroup = false -> SystemdCgroup = true
</blockquote>
<h3 id="a-few-things-you-should-know">
    <strong>Disable Swap</strong>
</h3>
<blockquote>
    nano /etc/fstab<br>
    /swap.img      none    swap    sw      0       0 -> #/swap.img      none    swap    sw      0       0
</blockquote>

<h3 id="a-few-things-you-should-know">
    <strong>Enable IP Forwarding</strong>
</h3>
<blockquote>
    nano /etc/sysctl.conf<br>
    #net.ipv4.ip_forward=1 -> net.ipv4.ip_forward=1
</blockquote>

<h3 id="a-few-things-you-should-know">
    <strong>Load Required Kernel Modules</strong>
</h3>
<blockquote>
    nano /etc/modules-load.d/k8s.conf<br>
    br_netfilter
</blockquote>

<h2 id="a-few-things-you-should-know">
    <strong>4. Install Kubernetes Packages</strong>
</h2>
<blockquote>
    - echo "deb [signed-by=/etc/apt/keyrings/kubernetes-apt-keyring.gpg] https://pkgs.k8s.io/core:/stable:/v1.28/deb/ /" | sudo tee /etc/apt/sources.list.d/kubernetes.list<br>
    - curl -fsSL https://pkgs.k8s.io/core:/stable:/v1.28/deb/Release.key | sudo gpg --dearmor -o /etc/apt/keyrings/kubernetes-apt-keyring.gpg<br>
    - apt update<br>
    - sudo apt install kubeadm kubectl kubelet
</blockquote>

<h2 id="a-few-things-you-should-know">
    <strong>5. Initialize Kubernetes Cluster</strong>
</h2>
<blockquote>
    sudo kubeadm init --control-plane-endpoint=<vm-address> --node-name k8s-ctrlr --pod-network-cidr=10.244.0.0/16
</blockquote>

<h3 id="a-few-things-you-should-know">
    <strong>Configure kubectl</strong>
</h3>
<blockquote>
    mkdir -p $HOME/.kube<br>
    sudo cp -i /etc/kubernetes/admin.conf $HOME/.kube/config<br>
    sudo chown $(id -u):$(id -g) $HOME/.kube/config
    </blockquote>

<h3 id="a-few-things-you-should-know">
    <strong>Deploy Flannel Network Plugin</strong>
</h3>
<blockquote>
    kubectl apply -f https://raw.githubusercontent.com/flannel-io/flannel/master/Documentation/kube-flannel.yml
</blockquote>

<h2 id="a-few-things-you-should-know">
    <strong>6. Join Worker Nodes</strong>
</h2>

<h3 id="a-few-things-you-should-know">
    <strong>Get join command from k8s-ctrlr</strong>
</h3>
<blockquote>
    kubeadm token create --print-join-command
</blockquote>

<h3 id="a-few-things-you-should-know">
    <strong>K8s-node join cluster</strong>
</h3>
<blockquote>
    kubeadm join <ctrlr-vm-address>:6443 --token lgmu8y.jp21mqh04f5o76zw --discovery-token-ca-cert-hash sha256:XXXXXXXXXXXXXXXXXXXXXXXXXXXXXXXXXXXXXX
</blockquote>
