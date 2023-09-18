## OpenLSP

### What's an LSP?

A Lighting Service Provider is a way to standardise the way that bitcoin services communicate with each other. Current implementations vary so this attempts to find a middle way.

### What is this?

This is an opinionated guide to setting up a bitcoin lightning node on a raspberry pi 4 with 8GB of RAM. Think of it as a reference that you can copy from rather than a complete solution that you deploy verbatim.

### Why?

There are a couple of approaches (raspiBlitz and Umbrel amongst others) to running personal lightning nodes. I've never been totally happy with the appoach, preferring to understand exactly what is happen and also to deploy into Kubernetes. 

On the LSP end, we have Greenlight and Breeze. Both are great, but I'd like to see a more standardised approach to how these services are deployed.

The end goal of this shoulnd't be so much to run a node, but more as a way to test out bitcoin-related projects inside of Kubernetes with the end goal of running such services in a production environment based off the same principles as a smaller node.

### What does this do?

Currently this contains all the commands to get bitcoin and lightning sevices running on a Raspberry Pi 4 with 8GB of RAM running Ubuntu. 

It uses the microk8s flavour of Kubernetes and includes the deployment files to get the following services running:
- bitcoind
- LND
- instrumentation and monitoring (Prometheus & Grafana) 
- Nginx ingress controller
- Cert-manager
- Tor
- ipv6 support

Next up:
- CLN
- BTCPayServer
- Channel management (RTL, Thunderhub, Ride the Lightning)


### What's the end goal?
looking at LSP standardisation and trying to build utop the existing services to provide a more standardised approach to bitcoin services in Kubernetes.

### Why microk8s?

Mostly becease everything else I tested seemed to have issues with ARM64/ipv6/be brittle etc. I hold no strong convictions about microk8s, but it seems to be the least brittle of the options I've tried.

I'd like to test KinD as well, but it doesn't support ARM64 yet, as well as k3s, minikube etc. But for the moment this is good enough and the focus is more on wiring up bitcoin-related service rather than Kubernetes. 

## Setup 

### Disk Partitioning

Two issues with disk space:
- the root partition is too small
- channels can close if the disk fills up (solved by creating a dedicated partition for the lightning state)

```bash
cat /etc/fstab
...
/dev/mapper/nvmedisk-home       /home                btrfs   defaults,discard 0 1  
/dev/mapper/nvmedisk-containers /var/lib/containers  btrfs   defaults,discard 0 1 
/dev/mapper/nvmedisk-microk8s   /var/snap/microk8s   btrfs   defaults,discard 0 1 
/dev/mapper/nvmedisk-blockchain /srv/blockchain      btrfs   defaults,discard 0 1 
...

sudo mkdir -p /var/snap/microk8s
sudo mkdir -p /srv/blockchain
sudo mount -a
```

### Namespaces

All services are deployed into the `openlsp` namespace.

### Boot Options

Most Pi distros don't ship with cgroups enabled. This is required for Kubernetes to work. In `/boot/firmware/cmdline.txt` and add the following settings at the end of the line, not in a new line, but at the end of the first line:

```
cgroup_enable=cpuset cgroup_enable=memory cgroup_memory=1
```

### Install microk8s:

```bash
echo ip_conntrack | sudo tee -a /etc/modules
sudo modprobe ip_conntrack
sudo apt update && sudo apt upgrade -y && sudo apt autoremove -y
sudo apt install linux-modules-extra-raspi # needed for Calico CNI
sudo snap install microk8s --classic --channel=1.28/stable
sudo usermod -a -G microk8s $USERNAME
newgrp microk8s
```

### Configure microk8s

```bash
microk8s enable dns ingress observability cert-manager hostpath-storage
```

### Configure kubectl

```bash
microk8s config > ~/.kube/config
# add the following to ~/.bashrc
export KUBECONFIG=$HOME/.kube/config
```

### Configure kubernetes

```bash
kubectl get pods --all-namespaces

# add instructions for ipv6 ingress
```

### Add a recent blockchain copy

Save time by copying the blockchain from another node. You will need `block`, `chainstate` and `indexes` directories.

```bash
rsync -av --delete --dry-run blocks indexes chainstate username@lspnode:/srv/blockchain/
```


### configure remote kubectl

```bash
microk8s config > ~/.kube/config
# copy the config to your local machine

# add the following to ~/.zshrc or .bashrc as appropriate
source <(kubectl completion zsh)
source <(kubectl completion bash)
```
alternatively [Lens](https://docs.k8slens.dev/) makes a nice Kubernetes GUI

### Configure observability

get the grafana password with
```bash
kubectl -n observability get secrets kube-prom-stack-grafana -o jsonpath="{.data.admin-password}" | base64 --decode ; echo 
```

Watch it with:
```bash
kubectl port-forward -n observability services/kube-prom-stack-grafana --address 0.0.0.0 3000:80 # grafana

kubectl port-forward -n observability service/prometheus-k8s --address 0.0.0.0 9090 # prometheus
```

### Cluster health

```bash
journalctl -f -u snap.microk8s.daemon-kubelite
```

## deplay to kubernetes

At this point our cluster should all be working and we can actually deploy the services.

```bash
kubectl apply -f deployments/namespace
kubectl apply -f deployments/bitcoind
kubectl apply -f deployments/lnd
```
### Security

Assume none. This is not designed to be used in production or hold more than test funds.

### Secrets management

The following secrets are required for the services to work:

- lnd-tls.cert
- lnd-admin.macaroon
- lnd-read-only.macaroon
- lnd-invoice.macaroon
- lnd-signer.macaroon
- lnd-chain-notifier.macaroon
- lnd-walletkit.macaroon
- lnd-backup.macaroon
- lnd-readonly.macaroon
- bitcoind-rpc.cert
- bitcoind-rpc.cookie
- bitcoind-rpc.username
- bitcoind-rpc.password
- bitcoind-zmqpubrawblock
- bitcoind-zmqpubrawtx
- bitcoind-zmqpubhashtx
- bitcoind-zmqpubhashblock


Todo: 

bitcoind ingress - permit inbound connections
lnd ingress - permit inbound connections
