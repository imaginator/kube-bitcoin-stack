```bash
kubectl exec -i -t -n bitcoin bitcoind-0 -c bitcoind -- sh -c "clear; (bash || ash || sh)"    
bitcoin-cli -conf=/data/.bitcoin/bitcoin.conf getblockchaininfo
```