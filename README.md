# rpi-k8s

Kubernetes Configuration for Rasberry PI 


## Configuration Rasberry PI SDCard

### Downdload Image 
* http://cdimage.ubuntu.com/releases/19.10.1/release/ubuntu-19.10.1-preinstalled-server-arm64+raspi3.img.xz?_ga=2.138453235.1165780893.1579841071-1013766512.1579094257


### Burning SD Card
* Burniing by etcher 
* https://www.balena.io/etcher/

### Configuration Default 

```
 $ touch /Volumes/boot/ssh.txt
 $ vi network-config
```
    
## Ubuntu Configuration 

* Default Password : ubuntu/ubuntu

### Password

```bash
$ passwd 
```

### Package 

```
$ sudo apt-get update && apt-get upgrade
```


## Rasbian Config

```
$ sudo raspi-config
```

### Locale 
```bash
$ sudo apt-get install language-pack-ko
$ sudo locale-gen ko_KR.UTF-8
$ sudo dpkg-reconfigure locales
$ sudo update-locale LANG=ko_KR.UTF-8 LC_MESSAGES=POSIX
```
### TimeZone

```
$ sudo timedatectl set-timezone 'Asia/Seoul'
```

### Change hostname 

```
$ sudo vi /etc/hostname
```

## Kubernetes 


### Install Docker 

```
# apt-get update && apt-get install \
  apt-transport-https ca-certificates curl software-properties-common

# curl -fsSL https://download.docker.com/linux/ubuntu/gpg | apt-key add -


```


```
$ sudo cat > /etc/modules-load.d/containerd.conf <<EOF
overlay
br_netfilter
EOF

$ sudo modprobe overlay
$ sudo modprobe br_netfilter

# Setup required sysctl params, these persist across reboots.
$ sudo cat > /etc/sysctl.d/99-kubernetes-cri.conf <<EOF
net.bridge.bridge-nf-call-iptables  = 1
net.ipv4.ip_forward                 = 1
net.bridge.bridge-nf-call-ip6tables = 1
EOF

$ sudo sysctl --system
```

```
$ sudo su - 
# apt-get update && apt-get install -y apt-transport-https ca-certificates curl software-properties-common

# curl -fsSL https://download.docker.com/linux/ubuntu/gpg | apt-key add -

# add-apt-repository \
    "deb [arch=arm64] https://download.docker.com/linux/ubuntu disco stable"

# apt-get update && apt-get install -y docker-ce

# mkdir -p /etc/containerd

# containerd config default > /etc/containerd/config.toml

```
/etc/containerd/config.toml
```
plugins.cri.systemd_cgroup = true
```

### Install kubernetes Component 

```
sudo apt-get install -y iptables arptables ebtables

# switch to legacy versions
sudo update-alternatives --set iptables /usr/sbin/iptables-legacy
sudo update-alternatives --set ip6tables /usr/sbin/ip6tables-legacy
sudo update-alternatives --set arptables /usr/sbin/arptables-legacy
sudo update-alternatives --set ebtables /usr/sbin/ebtables-legacy
```

```
sudo apt-get update && sudo apt-get install -y apt-transport-https curl
curl -s https://packages.cloud.google.com/apt/doc/apt-key.gpg | sudo apt-key add -
cat <<EOF | sudo tee /etc/apt/sources.list.d/kubernetes.list
deb https://apt.kubernetes.io/ kubernetes-xenial main
EOF
sudo apt-get update
sudo apt-get install -y kubelet kubeadm kubectl
sudo apt-mark hold kubelet kubeadm kubectl
```
/boot/firmware/nobtcmd.txt
```
cgroup_enable=cpu cgroup_enable=memory`
```

```
$ kubeadm config images pull

$ kubeadm init --config kubeadm-cfg.yaml


$ mkdir -p $HOME/.kube
  sudo cp -i /etc/kubernetes/admin.conf $HOME/.kube/config
  sudo chown $(id -u):$(id -g) $HOME/.kube/config
```


### Join Node

```
$ kubeadm token list

$ kubeadm token create

$ openssl x509 -pubkey -in /etc/kubernetes/pki/ca.crt | openssl rsa -pubin -outform der 2>/dev/null | openssl dgst -sha256 -hex | sed 's/^.* //'

$ kubeadm join 192.168.0.10:6443 --token 1h0h6r.js5labneufrdep32 --discovery-token-ca-cert-hash sha256:699de9df04b351246932615c82827f47c0aa9729fee1220eddad4ddeec9e9d68
```
