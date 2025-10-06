# Instalacion de Apache Hadoop en Lunix (Ubuntu)

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

# Instalación de Apache Hadoop en Ubuntu 24.04

Instalar y configurar Apache Hadoop 3.x en modo pseudo-distribuido en una máquina con Ubuntu 24.04. 

## Pre-Requisitos

- Sistema operativo: Ubuntu 24.04 LTS (recién instalado o actualizado).
- Java JDK 8 o 11: Hadoop 3.x es compatible con Java 8 y 11 (recomendado Java 11)..
- Un usuario con permisos sudo o root.

## Paso 1: Actualizar el sistema

```bash
sudo apt update && sudo apt upgrade -y
```

## Paso 2: Instalar Java (OpenJDK 11)

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
## Paso 3: Crear un usuario dedicado a Hadoop (opcional pero recomendado)

```bash
sudo adduser hadoop
sudo usermod -aG sudo hadoop
```

Cambia al usuario:

```bash
su - hadoop
```

## Paso 4: Configurar SSH sin contraseña (localhost)
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
