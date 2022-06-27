# argocd-demo
ArgoDC Demo


How to install ArgoCD

################################################################################################################################  
# get the cluster info first 

kubectl cluster-info

# This will create a new namespace, argocd, where Argo CD services and application resources will live.

kubectl create namespace argocd
kubectl apply -n argocd -f https://raw.githubusercontent.com/argoproj/argo-cd/v2.4.2/manifests/install.yaml

# Get all the namespace information 
kubectl -n argocd get all

# the argocd server if shown as ClusterIP, then we need to update it to NodePort or Loadbalancer or need to pass via ingress else it wont expose to outside network.
To edit type this command and go to all the way to the bottom and change ClusterIP to NodePort and save that

kubectl -n argocd edit svc argocd-server

# verify the updated cluster information

kubectl -n argocd get all

#Eg: 
$ kubectl -n argocd get all

# NAME                                                    READY   STATUS    RESTARTS   AGE
# pod/argocd-application-controller-0                     1/1     Running   0          2m5s
# pod/argocd-applicationset-controller-7449bc9bcc-bdpcf   1/1     Running   0          2m5s
# pod/argocd-dex-server-b874fbb76-vwsmf                   1/1     Running   0          2m5s
# pod/argocd-notifications-controller-75b88cd4f6-9gfqq    1/1     Running   0          2m5s
# pod/argocd-redis-558cfbbf7-qx2zt                        1/1     Running   0          2m5s
# pod/argocd-repo-server-7b995b67f5-pkcpq                 1/1     Running   0          2m5s
# pod/argocd-server-76dc65f7cb-5t5n2                      1/1     Running   0          2m5s

# NAME                                              TYPE        CLUSTER-IP       EXTERNAL-IP   PORT(S)                      AGE
# service/argocd-applicationset-controller          ClusterIP   10.100.226.7     <none>        7000/TCP,8080/TCP            2m6s
# service/argocd-dex-server                         ClusterIP   10.104.88.177    <none>        5556/TCP,5557/TCP,5558/TCP   2m6s
# service/argocd-metrics                            ClusterIP   10.100.69.107    <none>        8082/TCP                     2m6s
# service/argocd-notifications-controller-metrics   ClusterIP   10.104.31.201    <none>        9001/TCP                     2m6s
# service/argocd-redis                              ClusterIP   10.102.74.207    <none>        6379/TCP                     2m6s
# service/argocd-repo-server                        ClusterIP   10.101.79.253    <none>        8081/TCP,8084/TCP            2m6s
# service/argocd-server                             NodePort    10.96.101.169    <none>        80:32396/TCP,443:31672/TCP   2m6s
# service/argocd-server-metrics                     ClusterIP   10.107.142.140   <none>        8083/TCP                     2m6s

# NAME                                               READY   UP-TO-DATE   AVAILABLE   AGE
# deployment.apps/argocd-applicationset-controller   1/1     1            1           2m6s
# deployment.apps/argocd-dex-server                  1/1     1            1           2m6s
# deployment.apps/argocd-notifications-controller    1/1     1            1           2m5s
# deployment.apps/argocd-redis                       1/1     1            1           2m5s
# deployment.apps/argocd-repo-server                 1/1     1            1           2m5s
# deployment.apps/argocd-server                      1/1     1            1           2m5s

# NAME                                                          DESIRED   CURRENT   READY   AGE
# replicaset.apps/argocd-applicationset-controller-7449bc9bcc   1         1         1       2m6s
# replicaset.apps/argocd-dex-server-b874fbb76                   1         1         1       2m6s
# replicaset.apps/argocd-notifications-controller-75b88cd4f6    1         1         1       2m5s
# replicaset.apps/argocd-redis-558cfbbf7                        1         1         1       2m5s
# replicaset.apps/argocd-repo-server-7b995b67f5                 1         1         1       2m5s
# replicaset.apps/argocd-server-76dc65f7cb                      1         1         1       2m5s

# Get the node ip information

kubectl get nodes -o wide
  
# now open the node with port information here its 32396 for http and 31672 for https , see the output of service/argocd-server

NAME                                              TYPE        CLUSTER-IP       EXTERNAL-IP   PORT(S)                      AGE
service/argocd-server                             NodePort    10.96.101.169    <none>        80:32396/TCP,443:31672/TCP   2m6s

# Ui will open , ID = admin, password need to reset, 
For Argo CD v1.8 and earlier, the initial password is set to the name of the server pod, as per the getting started guide. For Argo CD v1.9 and later, the initial password is available from a secret named argocd-initial-admin-secret.
To change the password, edit the argocd-secret secret and update the admin.password field with a new bcrypt hash. You can use a site like https://www.browserling.com/tools/bcrypt to generate a new hash. For example:

# bcrypt(password)=<encrypted passowrd>
kubectl -n argocd patch secret argocd-secret \
  -p '{"stringData": {
    "admin.password": "<encrypted passowrd>",
    "admin.passwordMtime": "'$(date +%FT%T%Z)'"
	
	enter id= admin, password = you gave (not the encrypted one)
