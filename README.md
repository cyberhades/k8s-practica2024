## Práctica 2024
 
El objetivo de esta práctica es desplegar los ficheros proporcionados en este mismo repositorio.
Para ello necesitarás acceso a un clúster de Kubernetes, puedes usar uno local como `minikube`, o remoto.

La aplicación a desplegar consiste en 2 servicios y 2 deployments: una base de datos de Redis, y un contador de visitas. Los ficheros `app.yaml` y `redis.yaml`, están incompletos y necesitas definir la contraseña tanto para la base de datos Redis como para la aplicación. Dicha contraseña se especifica a través de una variable de entorno llamada `REDIS_PASS`.

Se pide lo siguiente:

#### 1. Creación de la contraseña

Para esta parte se pide la creación de un recurso del tipo `Secret` que contenga la contraseña que se usará para proteger la base de datos, la cual será también proporcionada al deployment visitas.

#### 2. Uso del secreto creado anteriormente en los deployments

Como se ha comentado, tanto la base de datos como la aplicación esperan una variable de entorno llamada `REDIS_PASS` cuyo valor sea la contraseña. Con lo cual se tendrán que modificar los ficheros `redis.yaml` y `app.yaml` para montar el secreto como variable de entorno.

## Puntuación de la práctica

- Creación del secreto - 3 puntos
- Modificación de `redis.yaml` para usar el secreto - 3 puntos
- Modificación de `app.yaml` para usar el secreto - 3 puntos
- Despliegue de la aplicación con funcionamiento correcto - 1 punto

## Notas

Los servicios (`redis-service.yaml` y `app-service.yaml`) no tienen que modificarse y deben de funcionar tal cual.

Si todo está desplegado de forma correcta, deberías de tener al menos los siguientes recursos en el nombre de espacio donde despliegues estos recursos:

```
$ kubectl get secrets
NAME           TYPE     DATA   AGE
redis-secret   Opaque   1      17s

$ kubectl get svc
NAME     TYPE        CLUSTER-IP     EXTERNAL-IP   PORT(S)          AGE
redis    ClusterIP   10.99.172.82   <none>        6379/TCP         27s
visits   NodePort    10.97.52.134   <none>        8080:30946/TCP   27s

$ kubectl get po
NAME                     READY   STATUS    RESTARTS      AGE
redis-749958f76f-2gklz   1/1     Running   0             33s
visits-9f9bb9579-d2hxg   1/1     Running   2 (31s ago)   33s
```

Como se puede ver tenemos un secreto, dos servicios y dos pods.

Para comprobar el funcionamiento de la aplicación podréis hacerlo desde el navegador o cualquier comando que permita hacer peticiones HTTP. Por ejemplo, con `curl` se podría hacer así:

    curl $(minikube ip):30946

Dónde el número del puerto (30946 en este ejemplo) es el `NodePort` del servicio. La respuesta de dicha llamada debería devolver un número (número de visitas), cada vez que se haga dicha llamada o refresco en el navegador, dicho número se incrementará en 1. Por ejemplo:

```
curl $(minikube ip):30946
1

curl $(minikube ip):30946
2

curl $(minikube ip):30946
3

curl $(minikube ip):30946
4
```
