LND depends on watching the blockchain using Bitcoind's RCP interface. For this it needs to know the `rpcpassword`. 

```bash
kubectl get secret bitcoind-rpcpassword -n bitcoind -o jsonpath='{.data}' | \
  jq -r 'to_entries | map("\(.key)=\(.value|@base64d)") | .[]' | \
  xargs -I{} kubectl create secret generic bitcoind-rpcpassword --from-literal={} -n lnd --dry-run=client -o yaml | \
  kubectl apply -f -
```

LND will need access to bitcoind's rcp passowrd secret
