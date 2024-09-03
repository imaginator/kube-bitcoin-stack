

```bash
mkdir -p /srv/kube-bitcoin-stack/fulcrum

kubectl get secret bitcoind-rpcpassword -n bitcoind -o jsonpath='{.data}' | \
  jq -r 'to_entries | map("\(.key)=\(.value|@base64d)") | .[]' | \
  xargs -I{} kubectl create secret generic bitcoind-rpcpassword --from-literal={} -n fulcrum --dry-run=client -o yaml | \
  kubectl apply -f -
```
