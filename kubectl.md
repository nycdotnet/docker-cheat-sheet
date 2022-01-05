Kubectl Cheat Sheet
===================

`kubectl get pods`  lists all pods in default namespace.
`kubectl get pods --namespace foo`  lists all pods in namespace `foo`.

`kubectl get deployment` lists all deployments in default namespace

`kubectl scale deployments/myservice --replicas 3` scales the `myservice` deployment to 3 replicas (this could be up or down)

`kubectl get secret` lists all secrets in default namespace  (the number in the third column is the count of secret key value pairs in the file)
`kubectl edit secret foo` edits the secret named `foo` in the default namespace using the default text editor.

`kubectl get configmap` lists all configmaps in the default namespace
`kubectl edit configmap foo` edits the `foo` configmap in the default namespace using the default text editor.

`kubectl apply -f mymanifest.yml` applies the manifest in the specified yaml file, such as a secret, configmap, etc.


