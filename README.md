# K10-4.0.2-for-OCP-3.11
# K104.0.2-OCP 3.11
# Pre-requisites
# You must install these CRD(s) before installing Kasten K10 4.0.2 in your OCP environment.
# kubectl apply -f bootstraps-dist-kio-kasten-io.yaml
# kubectl apply -f clusters-dist-kio-kasten-io.yaml
# kubectl apply -f distributions-dist-kio-kasten-io.yaml
# kubectl apply -f k10clusterrolebindings-auth-kio-kasten-io.yaml
# kubectl apply -f k10clusterroles-auth-kio-kasten-io.yaml
# kubectl apply -f policies-config-kio-kasten-io.yaml
# kubectl apply -f profiles-config-kio-kasten-io.yaml



# helm install k10 kasten/k10 --namespace kasten-io --version 4.0.2
