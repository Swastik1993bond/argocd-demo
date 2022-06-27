# argocd-demo
ArgoDC Demo

![image](https://user-images.githubusercontent.com/84220333/175857423-3f3a12b9-efd3-40d8-98e8-18eb6684bb4e.png)


How to install ArgoCD

################################################################################################################################  
# Get the cluster info first 

kubectl cluster-info

# This will create a new namespace, argocd, where Argo CD services and application resources will live.

kubectl create namespace argocd

kubectl apply -n argocd -f https://raw.githubusercontent.com/argoproj/argo-cd/v2.4.2/manifests/install.yaml

# Get all the namespace information 

kubectl -n argocd get all

# The argocd server if shown as ClusterIP, then we need to update it to NodePort or Loadbalancer or need to pass via ingress else it wont expose to outside network.
To edit type this command and go to all the way to the bottom and change ClusterIP to NodePort and save that

kubectl -n argocd edit svc argocd-server

# verify the updated cluster information

kubectl -n argocd get all

# Get the node ip information

kubectl get nodes -o wide
  
# Now open the node with port information here its 32396 for http and 31672 for https , see the output of service/argocd-server

NAME                                              TYPE        CLUSTER-IP       EXTERNAL-IP   PORT(S)                      AGE

service/argocd-server                             NodePort    10.96.101.169    <none>        80:32396/TCP,443:31672/TCP   2m6s

# Ui will comeup , id = admin, password need to reset, 
For Argo CD v1.8 and earlier, the initial password is set to the name of the server pod, as per the getting started guide. For Argo CD v1.9 and later, the initial password is available from a secret named argocd-initial-admin-secret. To change the password, edit the argocd-secret secret and update the admin.password field with a new bcrypt hash. You can use a site like https://www.browserling.com/tools/bcrypt to generate a new hash. For example:
#bcrypt(password)=<encrypted passowrd>

kubectl -n argocd patch secret argocd-secret \
-p '{"stringData": {
"admin.password": "<encrypted passowrd>",
"admin.passwordMtime": "'$(date +%FT%T%Z)'"
	
# Enter id= admin, password = you set preveiously (not the encrypted one)
