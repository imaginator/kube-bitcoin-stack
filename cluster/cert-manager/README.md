    Step 1: Add the JetStack Helm repository:

helm repo add jetstack https://charts.jetstack.io

    Step2: Fetch the latest charts from the repository:

helm repo update

    Step 3: Create namespace

kubectl create namespace cert-manager

    Step 3: Install Cert-Manager

helm install cert-manager jetstack/cert-manager --namespace cert-manager --set installCRDs=true

    Step 4: Confirm that the deployment succeeded, run:

      kubectl -n certmanager get pod

