
# Comandos esenciales de Kubernetes (kubectl)

Guía de referencia rápida con los comandos más usados de `kubectl` para gestionar un cluster de Kubernetes.

---

## Configuración e información del cluster

```bash
kubectl version                     # Versión de cliente y servidor
kubectl cluster-info                # Información general del cluster
kubectl config view                 # Ver configuración actual (kubeconfig)
kubectl config get-contexts         # Listar contextos disponibles
kubectl config use-context <nombre> # Cambiar de contexto/cluster
kubectl get nodes                   # Listar nodos del cluster
kubectl describe node <nombre>      # Detalles de un nodo específico
```

---

## Pods

```bash
kubectl get pods                          # Listar pods (namespace actual)
kubectl get pods -A                       # Listar pods de todos los namespaces
kubectl get pods -o wide                  # Info extendida (IP, nodo, etc.)
kubectl describe pod <nombre>             # Detalles de un pod
kubectl logs <nombre>                     # Ver logs de un pod
kubectl logs -f <nombre>                  # Ver logs en tiempo real (follow)
kubectl logs <nombre> -c <contenedor>     # Logs de un contenedor específico
kubectl exec -it <nombre> -- /bin/bash    # Entrar a un pod (shell interactiva)
kubectl delete pod <nombre>               # Eliminar un pod
kubectl port-forward <nombre> 8080:80     # Redirigir puerto local al pod
```

---

## Deployments

```bash
kubectl get deployments                          # Listar deployments
kubectl describe deployment <nombre>             # Ver detalles
kubectl create deployment <nombre> --image=<img>  # Crear deployment
kubectl scale deployment <nombre> --replicas=3    # Escalar réplicas
kubectl rollout restart deployment <nombre>       # Reiniciar
kubectl rollout status deployment <nombre>        # Ver estado del despliegue
kubectl rollout history deployment <nombre>       # Historial de versiones
kubectl rollout undo deployment <nombre>          # Revertir al despliegue anterior
kubectl delete deployment <nombre>                # Eliminar deployment
```

---

## Services
### ClusterIP

Genera un Service accesible únicamente desde dentro del clúster.

```bash
kubectl create service clusterip products-service \
  --tcp=3000:3000 \
  --dry-run=client -o yaml > service.yml
```

---

### NodePort

Genera un Service accesible desde el exterior a través de un puerto expuesto en cada nodo del clúster.

```bash
kubectl create service nodeport products-service \
  --tcp=3000:3000 \
  --dry-run=client -o yaml > service.yml
```

---

### LoadBalancer

Genera un Service que solicitará a la infraestructura cloud la creación de un balanceador de carga externo.

```bash
kubectl create service loadbalancer products-service \
  --tcp=80:3000 \
  --dry-run=client -o yaml > service.yml
```



```bash
kubectl get svc                       # Listar servicios
kubectl describe svc <nombre>         # Detalles de un servicio
kubectl expose deployment <nombre> --port=80 --target-port=8080 --type=ClusterIP
kubectl delete svc <nombre>           # Eliminar servicio
```

---

## ReplicaSets

```bash
kubectl get replicaset                # Listar ReplicaSets
kubectl describe replicaset <nombre>  # Ver detalles
kubectl delete replicaset <nombre>    # Eliminar ReplicaSet
```

---

## ConfigMaps y Secrets

```bash
kubectl get configmaps                                  # Listar ConfigMaps
kubectl create configmap <nombre> --from-literal=clave=valor
kubectl create configmap <nombre> --from-file=archivo.conf

kubectl get secrets                                     # Listar Secrets
kubectl create secret generic <nombre> --from-literal=clave=valor
kubectl describe secret <nombre>                        # Ver detalles (sin exponer valores)
```

---

## Namespaces

```bash
kubectl get namespaces                     # Listar namespaces
kubectl create namespace <nombre>          # Crear namespace
kubectl delete namespace <nombre>          # Eliminar namespace
kubectl config set-context --current --namespace=<nombre>  # Cambiar namespace por defecto
```

---

## Archivos YAML (manifiestos)

```bash
kubectl apply -f archivo.yaml         # Crear/actualizar recursos desde un YAML
kubectl apply -f carpeta/             # Aplicar todos los YAML de una carpeta
kubectl delete -f archivo.yaml        # Eliminar recursos definidos en el YAML
kubectl diff -f archivo.yaml          # Ver diferencias antes de aplicar
kubectl get all                       # Ver todos los recursos del namespace actual
```

---

## Debug y troubleshooting

```bash
kubectl get events                          # Ver eventos recientes del cluster
kubectl top pod                             # Uso de CPU/memoria por pod (requiere metrics-server)
kubectl top node                            # Uso de CPU/memoria por nodo
kubectl describe pod <nombre>               # Revisar errores, eventos, estado
kubectl get pod <nombre> -o yaml            # Ver manifiesto completo aplicado
```

---

## Atajos útiles

```bash
kubectl get pods --watch              # Ver cambios en tiempo real
kubectl get pods -l app=mi-app        # Filtrar por label
kubectl edit deployment <nombre>      # Editar recurso directamente en vivo
kubectl explain pod.spec              # Documentación de un campo del recurso
```

---

## Tips rápidos

- Usa `-n <namespace>` en cualquier comando para especificar un namespace distinto al actual.
- `kubectl get <recurso> -o yaml` o `-o json` te da la definición completa del recurso.
- `kubectl api-resources` lista todos los tipos de recursos disponibles en el cluster.
- Combina con Helm (`helm install`, `helm upgrade`) cuando gestiones aplicaciones empaquetadas como Charts.

---

## Helm

Helm es el gestor de paquetes de Kubernetes. Permite empaquetar, versionar y desplegar aplicaciones completas usando Charts.

### Repositorios

```bash
helm repo add <nombre> <url>          # Agregar un repositorio de charts
helm repo update                      # Actualizar la lista de charts disponibles
helm repo list                        # Listar repositorios agregados
helm search repo <nombre>             # Buscar un chart en los repositorios
```

### Charts y releases

```bash
helm create <nombre>                        # Crear un nuevo chart desde una plantilla
helm install <release> <chart>              # Instalar un chart como una nueva release
helm install <release> <chart> -f values.yaml   # Instalar usando un archivo de valores personalizado
helm install <release> <chart> --set clave=valor # Sobrescribir un valor puntual
helm upgrade <release> <chart>              # Actualizar una release existente
helm upgrade --install <release> <chart>    # Instalar si no existe, o actualizar si ya existe
helm uninstall <release>                    # Eliminar una release
helm list                                   # Listar releases activas
helm list -A                                # Listar releases en todos los namespaces
```

### Inspección y depuración

```bash
helm status <release>                 # Ver estado de una release
helm get values <release>             # Ver los valores usados en la release
helm get manifest <release>           # Ver los YAML generados/aplicados
helm show values <chart>              # Ver los valores por defecto de un chart
helm show chart <chart>               # Ver metadata del chart (Chart.yaml)
helm template <chart>                 # Renderizar los templates sin instalar (dry run local)
helm install <release> <chart> --dry-run --debug  # Simular instalación mostrando el YAML resultante
helm lint <chart>                     # Validar la estructura y sintaxis de un chart
```

### Versionado y rollback

```bash
helm history <release>                # Ver historial de revisiones de una release
helm rollback <release> <revisión>    # Volver a una revisión anterior
```

### Empaquetado

```bash
helm package <carpeta-chart>          # Empaquetar un chart en un .tgz
helm push <chart>.tgz <repo>          # Subir un chart a un repositorio (requiere plugin push)
```

### Estructura típica de un chart

```
mi-chart/
  Chart.yaml          # Metadata del chart (nombre, versión, descripción)
  values.yaml          # Valores por defecto
  charts/              # Subcharts o dependencias
  templates/           # Plantillas YAML (Deployment, Service, etc.)
    deployment.yaml
    service.yaml
    _helpers.tpl       # Funciones y snippets reutilizables
```