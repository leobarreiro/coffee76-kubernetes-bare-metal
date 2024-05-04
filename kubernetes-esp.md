# Kubernetes Bare Metal

## Setup
Siguiendo los pasos de este documento, vamos a instalar y configurar:

- Kubernetes - versión 1.30 más reciente
- Calico - driver de Red para Kubernetes Bare Metal
- metalLB - administrador de IP para kubernetes bare metal
- NGINX-Ingress - servicio de ingress para nuestros DNS
- LongHorn - servicio de almacenamiento persistente

### Kubernetes 

https://kubernetes.io/releases/

Seguir los pasos descriptos en el enlace abajo para configurar los repositorios apt en tu servidor linux:

https://kubernetes.io/docs/tasks/tools/install-kubectl-linux/#install-using-native-package-management

Paso a paso para instalación de kubernetes bare metal en Ubuntu Server:

https://www.itzgeek.com/how-tos/linux/ubuntu-how-tos/install-kubernetes-on-ubuntu-22-04.html

#### Permitir que el master node reciba pods de los servicios

https://medium.com/@shyamsandeep28/scheduling-pods-on-master-nodes-7e948f9cb02c


### Calico
Calico es un driver de red para kubernetes. Él provee networking y políticas de red para deployments auto-gerenciados.

https://docs.tigera.io/calico/latest/getting-started/kubernetes/self-managed-onprem/onpremises


### MetalLB

#### Instalación

https://metallb.universe.tf/installation/

#### IP Address Pool

https://metallb.universe.tf/configuration/

Creación de un IPAddressPool 

```
apiVersion: metallb.io/v1beta1
kind: IPAddressPool
metadata:
  name: first-pool
  namespace: metallb-system
spec:
  addresses:
  - 192.168.1.240-192.168.1.250
```


#### L2Advertisement

https://metallb.universe.tf/configuration/

```
apiVersion: metallb.io/v1beta1
kind: L2Advertisement
metadata:
  name: example
  namespace: metallb-system
spec:
  ipAddressPools:
  - first-pool
```


#### Compartir IP entre Servicios


https://metallb.universe.tf/usage/#ip-address-sharing

```
apiVersion: v1
kind: Service
metadata:
  name: dns-service-tcp
  namespace: default
  annotations:
    metallb.universe.tf/allow-shared-ip: "some-key-to-share"
```


### Nginx-ingress

https://docs.nginx.com/nginx-ingress-controller/installation/installing-nic/installation-with-helm/#pulling-the-chart

### LongHorn

#### Requerimientos 
https://longhorn.io/docs/archives/1.4.2/deploy/install/#installation-requirements

#### Revisar el entorno antes de instalar LongHorn

https://longhorn.io/docs/archives/1.4.2/deploy/install/#installation-requirements-requirements

#### Instalar con Helm

https://longhorn.io/docs/archives/1.4.2/deploy/install/install-with-helm/

#### Navegar por la interfaz de LongHorn

```
kubectl -n longhorn-system port-forward svc/longhorn-frontend --address 0.0.0.0 8080:80
```
