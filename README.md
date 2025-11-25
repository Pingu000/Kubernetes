# Kubernetes

Mi usuario de docker es: alejandromontero1

# Apuntes del profesor:

TEORÍA:

CLUSTER: grupo de ordenadores conectados en red

Caracterisitcas de un CLUSTER / DDP ETC - Examen

¿Que es un RPC? La lamada a una funcion remota que se suele hacer desde al cliente a un servidor remoto. La peticion tiene que contener el nombre de la funcion, los parametros y esperar el retorno.

Llamadas y gestion de procedimientos remotos

Tema 2: definicion de cluster y diapositiva 46: gestion de procesos distribuidos

Teoria de migracion de procesos

POD: contenedor en ejecucion 
DEPLOYMENTS: aplicacion completa formada por varios pods o contenedores en ejecución y con un servicio

Cluster de kubernetes: 

Contenedores: 

Kubernetes: gestor de contenedores que automatiza el despliegue, la gestión y la escalabilidad de aplicaciones.

Control-plane: master de kubernetes, es donde se ubica el nodo maestro

Nodo: maquina fisica que puede albergar varios pods




COMANDOS KUBERNETES:

kubectl get nodes/pods/deplyments/services/nodes

Crear deplyments (buscar comando en el pdf)

kubectl expose deployment kubernetes-bootcamp --type="NodePort" --port 8080
Luego hacer kubectl bet service para ver en que puerto FISICO se redirige el 8080.

curl localhost:30187
Para ver si te contesta el POD si esta bien deployeado, si tienes 3 pods pues cada vez te responde una

kubectl delete deployment kubernetes-bootcamp
Para eliminar todo el bootcamp y sus pods, pero no se borran los services que hayan sido levantados con ese bootcamp

kubectl delete service kubernetes-bootcamp
Elimina el servicio de ese bootcamp

kubectl exec -ti <pod> -- bash
Entrar dentro del POD

kubectl describe pod <pod>



ARCHIVOS YAML DE DEPLOYMENT:

kubectl apply -f <archivo.yaml>
Para ejecutar un yaml 

sudo nano bootcampDeployment.yml
Crear el arcivo yaml y pego el ejemplo del pdf pagina 35


CAMPOS: (en el examen pide rellenar los campos)

metadata.name: nombre del deplyment

sepc.replicas: numero de pods que crea

sepc.selector.matchlabels.app: etiqueta del deployment/aplicacion

template.metadata.labels.app: etiqueta del pod (los pods se crean con ese nombre)


ARCHIVOS YAML DE SERVICE:

nano bootcampService.yml
Crear el archivo yaml (pdf pag 36)

kubectl apply -f <archivo.yaml>
Para ejecutar un yaml 


CAMPOS:

selector.app: busca la etiqueta del deplyment 

metadata.name: nombre del servicio

targetPort: puerto del pod

nodePort: puerto real que vamos a enlazar al virtual del pod



PARA COMPROBAR SI FUNCIONA EL SERVICIO:

curl <ip_elastica>:31000
o
curl <ip_privada_del_pod>:31000
o
curl localhost:31000



CREAR DIRECTORIOS PERSISTENTES: (Gestionar sistemas de ficheros compartidos entre pods, para que al crear archivos es uno también se creen en el resto, y permanentes, que no se borren al reiniciar)

Para hacerlo, hay que añadir unas lineas en el archivo bootcampDeployment.tml (pdf pagina 38)

volumeMounts.mountPath: crea un directorio en todos los probs que se van a crear

hostPath.path: enlaza el directorio de arriba con uno real fisico (habria que crearlo antes de ejecutar el archivo)

Ahora al crear un archivo dentro de la carpeta de prueba dentro de cualquier pod tambien se creará el mismo archivo en el directorio fisico: /home/ubuntu/compartido y en el resto de pods dentro de la misma carpeta prueba.



CREAR NODOS (maquinas nuevas esclavas):

./kub_addNode.sh [IP DE LA MAQUINA NUEVA]: crea el nodo nuevo
Este archivo esta dentro de la carpeta kub que ya viene creada.


Desde el directorio kub puedes ejecutar comandos en los otros nodos:
ssh -i labsuser.pem <nombre del nodo> [Comandos que quieras]
Ejemplo:
:~/kub $ ssh -i labsuser.pem k8sslave1.psdi.org mkdir /home/ubuntu/compartido


Ahora cuando creas un deployment algunos pods se crean en el control-plane y otros en el nuevo nodo. Para ver dónde se han creado: 
kubectl describe pods | grep Node
kubectl get pods -o wide (otra opción mejor)

Lo mismo cuando creas servicios, ahora se crean en el nodo nuevo Y en el control-plane. Para verlo:
curl <IP_esclavo/control-plane>:31000
o
curl <IP del POD>:8080



COMANDOS NO TAN NECESARIOS:

kubeadm init: Inicia el servicio de kubernetes en el nodo máster. Configura el sistema de claves para poderse unir a la red virtual. Inicia el servicio kubelet del nodo máster para poder levantarpods/deployments en ese nodo

kubeadmin reset: Apaga el servicio kubernetes y destruye la red virtual creada con "kubeadminit". No borra los directorios de configuración creados en ejecuciones anteriores, hay que hacerlo manualmente si se quiere generar una nueva configuración.

kubeadm join: Se ejecuta desde un nodos "esclavo". Sirve para añadir un nodo nuevo a la red de kubernetes. Se necesita ejecutar en modo superusuario, y se debe ejecutar en un nodo que no tenga el servidor/master ejecutando




-------------- USO DE DOCKER: ---------------


COMANDOS:

docker login -u <nombre_de_usuario_de_docker>

docker images: listar imagenes
docker image rm <id>

docker pull <id>
docker push <id>


Crear una nueva imagen docker, es decir, un archivo docker. Tiene las siguientes secciones. Se llama Dockerfile. Ejemplo:

FROM ubuntu:20.04: plantilla de ubuntu que va a usar

RUN apt-get update
RUN apt-get install -y software-properties-common
Genéricas para actualizar dependencias

RUN apt-get update
RUN apt-get install -y apache2
Lo de arriba es para instalar apache2

EXPOSE 80: abrir puerto 80 que es el de apache
CMD apachectl -DFOREGROUND: comando usado para lanzar apache
COPY index.html /var/www/html/: copia los archivos a mi directorio de apache


PARA USARLO:

Creas un index.html con algo dentro, en el mismo directorio donde esta el archivo Dockerfile. Luego haces:

sudo docker build .   : para construir el dockerfile que acabamos de crear


SUBIR IMAGEN DE DOCKER 

docker build -q 
Te da un identificador en SHA. Copiar los 12 primeros caracteres

docker tag <IDENTIFICADOR12DIGS> <nombreUsuario>/<nombreImagen>:0.1v
nombre de la imagen en minuscula

docker images -a
Ya deberia salir

docker push <nombrequesalealhacerdockerimages-a>
Subirlo

Ahora tienes que modificar los archivos de deployment y de service (crear nuevos) y cambiar algunos parametros. Fotos en el chat de alvaro 20/11. Luego ejecutas los archivos.
