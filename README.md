# spring-cloud-data-flow-example
Repositorio para probar SCDF en profundidad

# Instalación
En este caso vamos a probar SCDF en Kubernetes, por lo que usaremos (Kind)[https://kind.sigs.k8s.io/docs/user/quick-start/#creating-a-cluster] para crearnos el cluster en local. https://kind.sigs.k8s.io/docs/user/quick-start/#creating-a-cluster

Es importante conocer la matriz de compatibilidad de SCDF con Kubernetes para usar la imagen correcta de Kind. Para ello podemos visitar el siguiente (enlace)[https://kind.sigs.k8s.io/docs/user/quick-start/#creating-a-cluster]

A fecha 03/02/2023 la última versión de SCDF es compatible con 1.23, cosa que llama la atención ya que K8 1.23 se depreca este mismo mes. Por lo que:

```
kind create cluster --image=kindest/node:v1.23.13
```

Esperamos a que termine:

![image](https://user-images.githubusercontent.com/11572462/216551036-b8d53b24-c039-4233-a6ad-325b694fbf0f.png)

# Instalación de SCDF
La instalación básica la podemos hacer usando el Chart de Helm proporcionado por bitnami:

```
helm repo add bitnami-repo https://charts.bitnami.com/bitnami
helm install spring-cloud-data-flow-release bitnami-repo/spring-cloud-dataflow
```
Para customizar la instalación se pueden añadir los siguientes parámetros: https://dataflow.spring.io/docs/installation/kubernetes/helm/#parameters

Una vez instalado se puede visualizar los componentes desplegados:

```
manu@AT-5CD2144YCB:/mnt/c/Users/mgarcia.devinuesa$ kubectl get all
NAME                                                                  READY   STATUS            RESTARTS   AGE
pod/spring-cloud-data-flow-mariadb-0                                  1/1     Running           0          67s
pod/spring-cloud-data-flow-rabbitmq-0                                 1/1     Running           0          67s
pod/spring-cloud-data-flow-spring-cloud-dataflow-server-b4cb6fl2565   0/1     PodInitializing   0          68s
pod/spring-cloud-data-flow-spring-cloud-dataflow-skipper-755d8q5c7r   0/1     PodInitializing   0          68s

NAME                                                           TYPE        CLUSTER-IP      EXTERNAL-IP   PORT(S)                                 AGE
service/kubernetes                                             ClusterIP   10.96.0.1       <none>        443/TCP                                 5m9s
service/spring-cloud-data-flow-mariadb                         ClusterIP   10.96.156.62    <none>        3306/TCP                                68s
service/spring-cloud-data-flow-rabbitmq                        ClusterIP   10.96.133.113   <none>        5672/TCP,4369/TCP,25672/TCP,15672/TCP   68s
service/spring-cloud-data-flow-rabbitmq-headless               ClusterIP   None            <none>        4369/TCP,5672/TCP,25672/TCP,15672/TCP   68s
service/spring-cloud-data-flow-spring-cloud-dataflow-server    ClusterIP   10.96.77.22     <none>        8080/TCP                                68s
service/spring-cloud-data-flow-spring-cloud-dataflow-skipper   ClusterIP   10.96.108.197   <none>        80/TCP                                  68s

NAME                                                                   READY   UP-TO-DATE   AVAILABLE   AGE
deployment.apps/spring-cloud-data-flow-spring-cloud-dataflow-server    0/1     1            0           68s
deployment.apps/spring-cloud-data-flow-spring-cloud-dataflow-skipper   0/1     1            0           68s

NAME                                                                              DESIRED   CURRENT   READY   AGE
replicaset.apps/spring-cloud-data-flow-spring-cloud-dataflow-server-b4cb6ff8c     1         1         0       68s
replicaset.apps/spring-cloud-data-flow-spring-cloud-dataflow-skipper-755d856794   1         1         0       68s
```

## Acceso local
```
export SERVICE_PORT=$(kubectl get --namespace default -o jsonpath="{.spec.ports[0].port}" services spring-cloud-data-flow-spring-cloud-dataflow-server)
kubectl port-forward --namespace default svc/spring-cloud-data-flow-spring-cloud-dataflow-server ${SERVICE_PORT}:${SERVICE_PORT} &
echo "http://127.0.0.1:${SERVICE_PORT}/dashboard"
```

Para entender todos los componentes instalados, se recomienda visitar: 
https://dataflow.spring.io/docs/concepts/architecture/


