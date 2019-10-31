## Traefik en K8s



### Pre-requisitos

##### Descargar e instalar  kubectl y helm


#### kubectl 
- herramienta de linea de comandos para interactuar con clusters de kubernetes.

https://kubernetes.io/es/docs/tasks/tools/install-kubectl/

- descargar kubeconfig respectiva del cluster

#### helm 
- helm es un package manager para kubernetes , se puede definir, instalar y actualizar stacks de aplicaciones a travez de los llamados charts, una analogia seria verlo como un apt-get install app en kubernetes.

https://helm.sh/docs/using_helm/



# Pasos

- Inicializar helm 

```
kubectl -n kube-system create serviceaccount tiller &&
kubectl create clusterrolebinding tiller --clusterrole cluster-admin --serviceaccount=kube-system:tiller &&
helm init --service-account=tiller &&
helm init --upgrade

```


- instalar traefik en el cluster basado en el helm [chart oficial](https://github.com/helm/charts/tree/master/stable/traefik).

- sustituir domain.com por tu dominio en cuestion.
```
helm install stable/traefik --name traefik \
        --set dashboard.enabled=true,dashboard.domain=traefik.domain.com,rbac.enabled=true \
        --namespace kube-system
```

revisar que ya este desplegado el loadbalancer externo , bajo el parametro de EXTERNAL-IP.
```
kubectl get svc traefik --namespace kube-system -w
```

- Agregar el DNS traefik.dominio.com apuntando a la IP del loadbalancer en el panel DNS.


- Ingresar al dns en cuestion 

traefik.dominio.com

- Instalar algo mas , sustituir domain.com por tu dominio en cuestion.

######ejecutar comando adentro de carpeta kubernetes.
```
helm   upgrade  --install --set web.name="todoapp" \
        --set web.image="katacoda/docker-http-server" --set web.host="todoapp.domain.com" --wait --force todoapp todoapp/
```


- escalando deploy 
```
kubectl scale --replicas=3 deployment todoapp
```

- borrar deploy
```
helm delete todoapp --purge
```