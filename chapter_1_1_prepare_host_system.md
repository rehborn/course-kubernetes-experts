# Installation

## Prepare system packages

```bash
sudo apt-get install -y apt-transport-https ca-certificates curl gpg net-tools
```

## Install kubernetes basis

```bash
# If the directory `/etc/apt/keyrings` does not exist, it should be created before the curl command, read the note below.
# sudo mkdir -p -m 755 /etc/apt/keyrings

curl -fsSL https://pkgs.k8s.io/core:/stable:/v1.30/deb/Release.key | sudo gpg --dearmor -o /etc/apt/keyrings/kubernetes-apt-keyring.gpg
sudo chmod 644 /etc/apt/keyrings/kubernetes-apt-keyring.gpg
# Add repositories
echo 'deb [signed-by=/etc/apt/keyrings/kubernetes-apt-keyring.gpg] https://pkgs.k8s.io/core:/stable:/v1.30/deb/ /' | sudo tee /etc/apt/sources.list.d/kubernetes.list

# Install Kubernetes Packages
sudo apt-get update
sudo apt-get install -y kubelet kubeadm kubectl 
sudo apt-mark hold kubelet kubeadm kubectl
```

```bash
# Enable kubelet
sudo systemctl enable --now kubelet
```

## Install container runtime (CRI)

```bash
# Install container runtime
sudo apt-get install containerd
```

```bash
# Fix default configuration 
sudo mkdir -p /etc/containerd/
containerd config default | sudo tee /etc/containerd/config.toml
sudo sed -i 's/SystemdCgroup \= false/SystemdCgroup \= true/g' /etc/containerd/config.toml
```

```bash
sudo systemctl enable --now containerd
```

## Vorbereitung des Systems

```bash
# Prepare os
sysctl -w net.ipv4.ip_forward=1
sysctl -w net.ipv6.conf.all.forwarding=1

# or
echo 1 | sudo tee /proc/sys/net/ipv4/ip_forward
sudo vim /etc/sysctl.conf

# Uncomment the next line to enable packet forwarding for IPv4
#net.ipv4.ip_forward=1

# Uncomment the next line to enable packet forwarding for IPv6
#  Enabling this option disables Stateless Address Autoconfiguration
#  based on Router Advertisements for this host
#net.ipv6.conf.all.forwarding=1

```

```bash
# Make sure swap is disabled
cat /proc/swaps
```

```bash
sudo reboot
```
