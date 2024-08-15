
# kube-bitcoin-stack

Welcome to **kube-bitcoin-stack**! 🎉

This project sets up a collection of Bitcoin-related services on Kubernetes, including:

- **Bitcoind**: The reference Bitcoin node.
- **BTCPayServer**: A self-hosted payment processor.
- **Electrum**: A lightweight Bitcoin wallet.
- **LND**: Lightning Network Daemon for Bitcoin's Lightning Network.
- **CLN**: Core Lightning, another Lightning Network implementation.
- Plus other useful services and tools!

## Overview

This stack provides everything you need to run Bitcoin and Lightning Network services in a Kubernetes environment. It includes:

- **Simplified YAML files**: Easy to import into your own projects or use as-is.
- **Prometheus exporters**: For collecting metrics from all your services.
- **Prometheus server**: Configured to aggregate metrics.
- **Grafana dashboards**: To visualize operational and Bitcoin-related metrics.

### Features

- **Namespace Isolation**: Each service runs in its own Kubernetes namespace for better organization and management.
- **Production-Ready**: Ideal for production engineering of Bitcoin services.
- **Metrics & Monitoring**: Integrated Prometheus and Grafana for monitoring and visualization.

## Getting Started

1. **Clone the Repository**

```bash
git clone https://github.com/imaginator/kube-bitcoin-stack.git
cd kube-bitcoin-stack
```

2. **Apply Kubernetes Manifests**

Import the provided YAML files into your Kubernetes cluster:

```bash
kubectl apply -f kube-bitcoin-stack/deployments/<whatever>
```

3. **Access Services**

Each service runs in its own namespace. Check out the Kubernetes services and deployments:

```bash
kubectl get services --all-namespaces
kubectl get deployments --all-namespaces
```

4. **View Metrics**

Access the Grafana dashboard for metrics and monitoring:

```bash
kubectl port-forward svc/grafana 3000:80 -n <namespace>
```
Then navigate to http://localhost:3000 in your browser.

## Use Cases

- **Production Engineering**: Perfect for Site Reliability Engineers (SREs) or Production Engineers looking to manage Bitcoin and Lightning services.
- **Alternative to Raspiblitz/Umbrel**: Run your own Bitcoin stack with all the monitoring and management tools you need.

## Contributing

If you want to contribute to this project, feel free to fork the repository and submit a pull request. We welcome any improvements or suggestions!

## License

This project is licensed under the MIT License. See the [LICENSE](LICENSE) file for details.

## Support

For questions or support, open an issue on GitHub or contact us via the project's discussion forums.

Happy Bitcoin stacking^Whacking! 🚀
