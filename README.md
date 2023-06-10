

## IBM Entitlement Key



- Log in to Access your [IBM entitlement key](https://myibm.ibm.com/products-services/containerlibrary) with an IBMid and password associated with the entitled software.

- Select the View library option to verify your entitlement(s).

- Select the Get entitlement key to retrieve the key.

A Secret containing the entitlement key is created in the tools namespace.
```
oc new-project tools || true \
oc create secret docker-registry ibm-entitlement-key -n tools \
--docker-username=cp \
--docker-password="<ibm-entitlement-key>" \
--docker-server=cp.icr.io
```
The output will be:
![argocd_clean](images/secret-screen.png "Screenshot after first application in ArgoCD")

## Setting up environment variables for this tutorial
```
export GIT_ORG=#enter name for GitHub organization
echo $GIT_ORG
oc login --token=#token --server=#server
```

## Setting up GitOps 

### Clone your forked GitOps repositories
```
cd ~
mkdir $GIT_ORG
cd $GIT_ORG
git clone https://github.com/$GIT_ORG/multi-tenancy-gitops.git
git clone https://github.com/$GIT_ORG/multi-tenancy-gitops-infra.git
git clone https://github.com/$GIT_ORG/multi-tenancy-gitops-services.git
git clone https://github.com/$GIT_ORG/multi-tenancy-gitops-apps.git
ls -l
```

#### Install the `Red Hat OpenShift GitOps` operator
```
cd multi-tenancy-gitops
oc apply -f setup/ocp47
```

The response confirms that the below resources has been created:
```
clusterrole.rbac.authorization.k8s.io/custom-argocd-cluster-argocd-application-controller created
clusterrolebinding.rbac.authorization.k8s.io/openshift-gitops-argocd-application-controller created
clusterrolebinding.rbac.authorization.k8s.io/openshift-gitops-cntk-argocd-application-controller created
subscription.operators.coreos.com/openshift-gitops-operator created
```

Screenshot of operator

#### Apply customized ArgoCD instance
Instead of using the default ArgoCD from the `Red Hat OpenShift GitOps` operator we want to manage if via GitOps. We will delete it so we can apply our customized ArgoCD.
```
oc delete gitopsservice cluster -n openshift-gitops || true
oc apply -f setup/ocp47/argocd-instance/ -n openshift-gitops
```

#### Launch and login to ArgoCD
the Following command will provide ArgoCD URL
```
#ARGOCD username: admin
#ARGOCD URL:
oc get route -n openshift-gitops | grep openshift-gitops-cntk-server | awk '{print "https://"$2}'
#ARGOCD_PASSWORD: 
oc get secret/openshift-gitops-cntk-cluster -n openshift-gitops -o json | jq -r '.data."admin.password"' | base64 -D
```

Screenshot of blank ArgoCD

 ![argocd_clean](images/argocd_clean.jpg "Screenshot of clean ArgoCD")

  
## Managing infrastructure
[Additional Info](https://production-gitops.dev/guides/cp4i/mq/cluster-config/gitops-config/#the-sample-gitops-repository)
### Customizing the GitOps repositories

#### Run the customization script
```
./scripts/set-git-source.sh
```



