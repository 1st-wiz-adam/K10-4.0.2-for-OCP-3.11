# K10-4.0.2-for-OCP-3.11
K104.0.2-OCP 3.11
# Pre-requisites
You must install these CRD(s) before installing Kasten K10 4.0.2 in your OCP environment.  Download them from this library and place them in the appropriate repo on your OCP cluster for installation.
```
Kubernetes v 1.11
Install Helm 
https://helm.sh/docs/intro/install/
Place the CRD(s) in the appropriate cluster to install them follow the command guides below (make sure you change your filepath to match your own environment before applying)
kubectl apply -f bootstraps-dist-kio-kasten-io.yaml
kubectl apply -f clusters-dist-kio-kasten-io.yaml
kubectl apply -f distributions-dist-kio-kasten-io.yaml
kubectl apply -f k10clusterrolebindings-auth-kio-kasten-io.yaml
kubectl apply -f k10clusterroles-auth-kio-kasten-io.yaml
kubectl apply -f policies-config-kio-kasten-io.yaml
kubectl apply -f profiles-config-kio-kasten-io.yaml
```

# Helm commands to install specific version of K10 that works for OCP 3.11
```
More Information on K10 
https://docs.kasten.io/latest/install/requirements.html

You do not need to run the pre-flight scripts found on the URL above; they will not execute properly on OCP 3.11.
helm repo add kasten https://charts.kasten.io/
helm repo update
kubectl create namespace kasten-io
helm install k10 kasten/k10 --namespace kasten-io --version 4.0.2
```

# Additional notes for consideration before migrating applications over to a newer version of OCP 4.x
```
Read more on (Generic Storage Backup and Restore)
https://docs.kasten.io/latest/install/generic.html
Infrastructure Profiles or Injecting sidecar will be necessary in order to succesfully backup workloads from OCP 3.11.  You can use either one but both have limitations.
