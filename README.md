# Instalacion de Apache Hadoop en Lunix

Apache Hadoop es un marco de trabajo (framework) de código abierto diseñado para el almacenamiento distribuido y el procesamiento de grandes volúmenes de datos (Big Data) en clústeres de computadoras. Está construido para escalar desde un solo servidor hasta miles de máquinas, cada una con almacenamiento y capacidad de cómputo local.

## Componentes Principales 
### 1. HDFS (Hadoop Distributed File System) 

#### Propósito: Sistema de archivos distribuido.
Características:
- Tolerante a fallos y altamente disponible.
- Almacena datos en bloques (típicamente de 128 MB en Hadoop 2+).
- Replica los datos (por defecto, 3 copias) en diferentes nodos para evitar pérdidas.

### 2. MapReduce 

#### Propósito: Modelo de programación para procesamiento distribuido.
Fases:
- Map: Procesa los datos de entrada y genera pares clave-valor.
- Reduce: Combina los resultados del map para producir la salida final.

Ideal para: Procesamiento por lotes (batch processing), no para tiempo real.

### 3. YARN (Yet Another Resource Negotiator) 

#### Propósito: Gestiona recursos y programa tareas en el clúster.
Componentes:
- ResourceManager: Coordinador central de recursos.
- NodeManager: Gestiona recursos en cada nodo.
- ApplicationMaster: Supervisa la ejecución de cada aplicación.

### 4. Hadoop Common 

Bibliotecas y utilidades compartidas por todos los módulos.

## Ecosistema de Hadoop 

Además de sus componentes centrales, Hadoop cuenta con un rico ecosistema: 

- Hive: Permite consultar datos con un lenguaje similar a SQL.
- Pig: Lenguaje de alto nivel para flujos de datos.
- HBase: Base de datos NoSQL para acceso en tiempo real.
- Spark: Motor de procesamiento rápido en memoria (más veloz que MapReduce).
- ZooKeeper: Servicio de coordinación distribuida.
- Sqoop: Transfiere datos entre Hadoop y bases de datos relacionales.
- Flume: Recoge y agrega logs o flujos de datos.
- Oozie: Programador de flujos de trabajo.

En este tutorial, aprenderá cómo instalar y configurar Hadoop en Ubuntu.
profile
Qwen3-Max 1:56 pm

# Paso A. Instalación de Apache Hadoop WSL2 (Ubuntu 24.04)

##  Instalar y activar WSL2 en Windows

Verificar que Windows sea versión 2004 o superior. Se puede revisar con el comando winver.

Abrir PowerShell como administrador y habilitar WSL con:

```bash
dism.exe /online /enable-feature /featurename:Microsoft-Windows-Subsystem-Linux /all /norestart
```
Habilitar la plataforma de máquina virtual con:

```bash
dism.exe /online /enable-feature /featurename:VirtualMachinePlatform /all /norestart
```

Reiniciar el equipo.

## Verificar WSL2
Establecer WSL2 como versión predeterminada:

```bash
wsl --set-default-version 2
```

Para listar las instancias o distribuciones de WSL (Windows Subsystem for Linux) instaladas en Windows, se utiliza el comando en la terminal o PowerShell:

```bash
wsl --list --verbose
```
o su versión corta:
```bash
wsl --list --verbose
```

Este comando muestra todas las distribuciones Linux instaladas, el estado de cada una (si está corriendo o detenida) y la versión de WSL que usan (WSL 1 o WSL 2). También se puede usar simplemente wsl --list para ver las distribuciones sin detalles adicionales.

Además, si se quiere solo listar las distribuciones que están corriendo, se emplea:

```bash
wsl --list --running
```
## Instalar ubuntu WSL2

### Método 1: Instalar desde la terminal con WSL

Abrir PowerShell como administrador.

Listar las distribuciones disponibles con:

```bash
wsl --list --online
```

Instalar Ubuntu 24.04 con:

```bash
wsl --install -d Ubuntu-24.04
```

Esperar a que finalice la instalación y luego abrir la distribución para completar la configuración inicial.

### Método 2: Instalar desde Microsoft Store

Abrir Microsoft Store en Windows.

Buscar "Ubuntu" y elegir "Ubuntu 24.04 LTS".

Hacer clic en "Obtener" o "Instalar".

Una vez instalada, abrir la aplicación para terminar la configuración.

### Método 3: Descargar el archivo .wsl y usar instalación manual

Descargar la imagen oficial de Ubuntu 24.04 en formato .wsl desde la página oficial de Ubuntu para WSL.

Instalar haciendo doble clic en el archivo o usando el comando:

```bash
wsl --install --from-file <ruta_del_archivo.wsl>
```
Completar la configuración inicial después de la instalación.

### Abrir una ventana de PowerShell o CMD y ejecutar el comando:

```bash
wsl -d Ubuntu-24.04
```

# Paso B. Instalación de Apache Hadoop en Ubuntu 24.04

Instalar y configurar Apache Hadoop 3.x en modo pseudo-distribuido en una máquina con Ubuntu 24.04. 

## 📋 Requisitos

- Sistema operativo: Ubuntu 24.04 LTS (recién instalado o actualizado).
- Java JDK 8 o 11: Hadoop 3.x es compatible con Java 8 y 11 (recomendado Java 11)..
- Un usuario con permisos sudo o root.

## 🧰 Paso 1: Actualizar el sistema

```bash
sudo apt update && sudo apt upgrade -y
```

## ☕  Paso 2: Instalar Java (OpenJDK 11)

Hadoop 3.x funciona bien con OpenJDK 11:

```bash
sudo apt install openjdk-11-jdk -y
```
Verifica la instalación:

```bash
java -version
```
Configura la variable de entorno JAVA_HOME:

```bash
echo 'export JAVA_HOME=/usr/lib/jvm/java-11-openjdk-amd64' >> ~/.bashrc
echo 'export PATH=$PATH:$JAVA_HOME/bin' >> ~/.bashrc
source ~/.bashrc
```
Verifica la instalación:

```bash
echo $JAVA_HOME
```
## 👤 Paso 3: Crear un usuario dedicado a Hadoop (opcional pero recomendado)

```bash
sudo adduser hadoop
sudo usermod -aG sudo hadoop
```

Cambia al usuario:

```bash
su - hadoop
```

## 🔐 Paso 4: Configurar SSH sin contraseña (localhost)
Hadoop necesita SSH para comunicarse con los nodos (aunque sea local). 

Instala SSH si no está: 
```bash
sudo apt install openssh-server openssh-client -y
```

Genera una clave SSH sin contraseña:

```bash
ssh-keygen -t rsa -P '' -f ~/.ssh/id_rsa
cat ~/.ssh/id_rsa.pub >> ~/.ssh/authorized_keys
chmod 0600 ~/.ssh/authorized_keys
```
Prueba el acceso sin contraseña:

```bash
ssh localhost
```
## 📦 Paso 5: Descargar e instalar Apache Hadoop
Ve al directorio de descargas o a tu home:
```bash
cd ~
```

Descarga la última versión estable de Hadoop (ejemplo con 3.3.6; verifica en https://hadoop.apache.org/releases.html ):

```bash
wget https://dlcdn.apache.org/hadoop/common/hadoop-3.4.2/hadoop-3.4.2.tar.gz
```
Descomprime:
```bash
tar -xvzf hadoop-3.4.2.tar.gz
mv hadoop-3.4.2 hadoop
```
## 🌐 Paso 6: Configurar variables de entorno de Hadoop

Edita ~/.bashrc:
```bash
sudo nano ~/.bashrc
```

Agrega al final:

```bash
# Hadoop
export HADOOP_HOME=/home/hadoop/hadoop
export HADOOP_INSTALL=$HADOOP_HOME
export HADOOP_MAPRED_HOME=$HADOOP_HOME
export HADOOP_COMMON_HOME=$HADOOP_HOME
export HADOOP_HDFS_HOME=$HADOOP_HOME
export YARN_HOME=$HADOOP_HOME
export HADOOP_COMMON_LIB_NATIVE_DIR=$HADOOP_HOME/lib/native
export PATH=$PATH:$HADOOP_HOME/sbin:$HADOOP_HOME/bin
export HADOOP_OPTS="-Djava.library.path=$HADOOP_HOME/lib/native"
```
Guarda y recarga:

```bash
source ~/.bashrc
```
Verifica:
```bash
echo $HADOOP_HOME
hadoop version
```
## ⚙️ Paso 7: Configurar archivos de Hadoop

Los archivos de configuración están en $HADOOP_HOME/etc/hadoop/.

### 7.1 Editar hadoop-env.sh
```bash
sudo nano $HADOOP_HOME/etc/hadoop/hadoop-env.sh
```
Busca la línea con export JAVA_HOME y cámbiala a:
```bash
export JAVA_HOME=/usr/lib/jvm/java-11-openjdk-amd64
```

### 7.2 Editar core-site.xml

```bash
sudo nano $HADOOP_HOME/etc/hadoop/core-site.xml
```

Dentro de las etiquetas < configuration >...</ configuration >, agrega:

```bash
<property>
    <name>fs.defaultFS</name>
    <value>hdfs://localhost:9000</value>
</property>
```
### 7.3 Editar hdfs-site.xml
```bash
sudo nano $HADOOP_HOME/etc/hadoop/hdfs-site.xml
```

Agrega:

```bash
<property>
    <name>dfs.replication</name>
    <value>1</value>
</property>
<property>
    <name>dfs.namenode.name.dir</name>
    <value>file:///home/hadoop/hadoop_data/hdfs/namenode</value>
</property>
<property>
    <name>dfs.datanode.data.dir</name>
    <value>file:///home/hadoop/hadoop_data/hdfs/datanode</value>
</property>
```

Crea los directorios:

```bash
mkdir -p ~/hadoop_data/hdfs/namenode
mkdir -p ~/hadoop_data/hdfs/datanode
```
### 7.4 Editar mapred-site.xml
```bash
sudo nano $HADOOP_HOME/etc/hadoop/mapred-site.xml
```

Agrega:
```bash
<property>
    <name>mapreduce.framework.name</name>
    <value>yarn</value>
</property>
```

### 7.5 Editar yarn-site.xml
```bash
sudo nano $HADOOP_HOME/etc/hadoop/yarn-site.xml
```
Agrega:

```bash
<property>
    <name>yarn.nodemanager.aux-services</name>
    <value>mapreduce_shuffle</value>
</property>
<property>
    <name>yarn.nodemanager.aux-services.mapreduce.shuffle.class</name>
    <value>org.apache.hadoop.mapred.ShuffleHandler</value>
</property>
```
## 🔄 Paso 8: Formatear el NameNode
Antes de iniciar HDFS por primera vez:
```bash
hdfs namenode -format
```
## ▶️ Paso 9: Iniciar servicios de Hadoop

```bash
start-dfs.sh
start-yarn.sh
```

Verifica los procesos:
```bash
jps
```
Deberías ver algo como:
```bash
NameNode
DataNode
SecondaryNameNode
ResourceManager
NodeManager
Jps
```

## 🌍 Paso 10: Acceder a las interfaces web

- HDFS Web UI: http://localhost:9870 
- YARN Web UI: http://localhost:8088 

## 🧪 Paso 11: Ejecutar un ejemplo de MapReduce

Crea un directorio en HDFS:

```bash
hdfs dfs -mkdir /user
hdfs dfs -mkdir /user/hadoop
```
Copia un archivo de ejemplo (puedes crear uno):

```bash
echo "Hola mundo Hadoop" > input.txt
hdfs dfs -put input.txt /user/hadoop/
```
Ejecuta el ejemplo de WordCount:

```bash
hadoop jar $HADOOP_HOME/share/hadoop/mapreduce/hadoop-mapreduce-examples-3.4.2.jar wordcount /user/hadoop/input.txt /user/hadoop/output
```
Ver resultados:

```bash
hdfs dfs -cat /user/hadoop/output/part-r-00000
```

## 🛑 Paso 12: Detener servicios (cuando termines)

```bash
stop-yarn.sh
stop-dfs.sh
```

# Autor
Derecho Reservados ® Jaime Llanos Bardales