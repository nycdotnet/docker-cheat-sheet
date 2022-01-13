Kubectl Cheat Sheet
===================
I know kubectl is not docker.  I don't care.  :-)

Official cheat sheet: https://kubernetes.io/docs/reference/kubectl/cheatsheet/

`kubectl version -o json` lists the version of kubectl you have installed and the version of the kubernetes cluster if you're connected.

`kubectl get pods`  lists all pods in default namespace.

`kubectl get pods --namespace foo`  lists all pods in namespace `foo`.

`kubectl get deployment` lists all deployments in default namespace

`kubectl describe deployment bar` describes deployment `bar` in the default namespace (stuff typically in the deployment.yml such as labels, annotations, images, resource request/limits, etc.)

`kubectl scale deployments/myservice --replicas 3` scales the `myservice` deployment to 3 replicas (this could be up or down)

`kubectl rollout restart deployment/myservice` deletes and restarts all pods in the existing deployment for myservice

`kubectl rollout status deployment/myservice` checks the current status of the rollout for myservice

`kubectl rollout history deployment/myservice` lists known revisions of the deployment of myservice

`kubectl get secret` lists all secrets in default namespace  (the number in the third column is the count of secret key value pairs in the file)

`kubectl edit secret foo` edits the secret named `foo` in the default namespace using the default text editor.

`kubectl get configmap` lists all configmaps in the default namespace

`kubectl edit configmap foo` edits the `foo` configmap in the default namespace using the default text editor.

`kubectl apply -f mymanifest.yml` applies the manifest in the specified yaml file, such as a secret, configmap, etc.


