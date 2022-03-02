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
helm install k10 kasten/k10 --namespace kasten-io --version 4.0.2 --set scc.create=true
```

# Additional notes for consideration before migrating applications over to a newer version of OCP 4.x
```
Read more on (Generic Storage Backup and Restore)
https://docs.kasten.io/latest/install/generic.html
Infrastructure Profiles or Injecting sidecar will be necessary in order to succesfully backup workloads from OCP 3.11.  You can use either one but both have limitations.

# Sidecar Injection Method
helm repo add kasten https://charts.kasten.io/
helm repo update
kubectl create namespace kasten-io
helm install k10 kasten/k10 --namespace kasten-io --version 4.0.2 --set scc.create=true --set injectKanisterSidecar.enabled=true (This command will inject sidecar into EVERTYHING in the cluster, please read the warning below before executing)

WARNING
It is recommended to add at least one namespaceSelector or objectSelector when enabling the injectKanisterSidecar feature. Otherwise, K10 will try to inject a sidecar into every new workload. In the common case, this will lead to undesirable results and potential performance issues.

For example, to inject sidecars into workloads that match the label component: db and are in namespaces that are labeled with k10/injectKanisterSidecar: true, the following options should be added to the K10 Helm install command:
--set injectKanisterSidecar.enabled=true \
--set-string injectKanisterSidecar.objectSelector.matchLabels.component=db \
--set-string injectKanisterSidecar.namespaceSelector.matchLabels.k10/injectKanisterSidecar=true

The labels set with namespaceSelector and objectSelector are mutually inclusive. This means that if both the options are set to perform sidecar injection, the workloads should have labels matching the objectSelector labels AND they have to be created in the namespace with labels that match the namespaceSelector labels. Similarly, if multiple labels are specified for either namespaceSelector or objectSelector, they will all needed to match for a sidecar injection to occur.

For the sidecar to choose a security context that can read data from the volume, K10 performs the following checks in order:
1. If the primary container has a SecurityContext set, it will be used in the sidecar. If there are multiple primary containers, the list of containers will be iterated over and the first one which has a SecurityContext set will be used.
2. If the workload PodSpec has a SecurityContext set, the sidecar does not need an explicit specification and will automatically use the context from the PodSpec.
3. If the above criteria are not met, by default, no SecurityContext will be set.
```

# Create a location profile for the migration
```
Additional Documentation: https://docs.kasten.io/latest/usage/configuration.html

In order to fail over an application you must have a location profile configured to move the application and it's resources outside of the 3.11 cluster.  

WARNING

We HIGHLY recommend that you leverage an S3 object storage for the location profile.  The NFS target in Kasten 4.0.2 was just released and we do not recommend it for this use case; in the updated version of K10 NFS target can be leveraged for future application migrations.

Our recommendations in order for application migration 
Option 1: Amazon S3
Option 2: S3 object storage (On Prem or provided via Partner)
Option 3: Gooogle Cloud Storage
```

# On the new OCP 4.x cluster (target cluster for the application)
```
Install K10 with the same version that you used previously on the older cluster.  You do not need to inject sidecar here.

helm repo add kasten https://charts.kasten.io/
helm repo update
kubectl create namespace kasten-io
helm install k10 kasten/k10 --namespace kasten-io --version 4.0.2 --set scc.create=true

Add the same S3 object location profile used in the 3.11 cluster.

```

# create a backup policy for the application that needs to be migrated (3.11 cluster)
```
Create a backup policy to protect the application, you should see sidecarinjected through CLI (or GUI) on the application

Backup the application and make sure the backup and export actions were succsefull.
```

# create an import policy in the 4.x cluster
```

On the 3.11 cluster
Copy the import details on the Backup Policy in the 3.11 cluster it will be needed in order to create the import policy.

On the 4.x cluster
Create an import policy
paste the import details in the field and select the S3 object storage that the location.

Run the import policy
```

# Restore the application



