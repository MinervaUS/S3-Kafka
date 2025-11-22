<a name="readme-top"></a>
# Clúster Hadoop + Kafka en Docker

Este repositorio contiene un entorno completo de desarrollo para **Big Data** que integra un clúster Hadoop (HDFS + YARN) con Apache Kafka, diseñado para procesamiento distribuido y streaming de datos en tiempo real.

## 🚀 Características  

- 🐳 **Entorno Dockerizado Simplificado**: Docker Compose optimizado con YAML anchors para fácil mantenimiento
- 📁 **Clúster Hadoop Completo**: 
  - HDFS con 1 NameNode y 2 DataNodes
  - YARN con ResourceManager y NodeManager
  - Configuración lista para MapReduce
- 📡 **Stack Kafka Completo**:
  - Apache Kafka con Zookeeper
  - Kafka Connect con conector HDFS preinstalado
  - Kafka UI para administración visual
- 🔒 **Volúmenes Persistentes**: Datos de Hadoop almacenados en volúmenes Docker nombrados
- ⚡ **Red Simplificada**: Comunicación entre servicios mediante DNS de Docker
- 📝 **Scripts Optimizados**: Inicialización robusta con manejo de errores
- 🛡️ **Configuración de Solo Lectura**: Archivos de configuración y scripts montados en modo read-only 


## 📂 Estructura del Proyecto  

```
📂 S3-Kafka/
├── 📄 docker-compose.yml              # Configuración principal del clúster
├── 📄 dockerfile                      # Imagen personalizada para YARN (Java 11)
├── 📄 Dockerfile.kafka-connect        # Imagen con conector HDFS para Kafka
├── 📄 README.md                       # Este archivo
│
├── 📂 hadoop_config/                  # Configuración de Hadoop
│   ├── 📄 core-site.xml              # Configuración central de Hadoop
│   ├── 📄 hdfs-site.xml              # Configuración de HDFS
│   ├── 📄 yarn-site.xml              # Configuración de YARN
│   ├── 📄 mapred-site.xml            # Configuración de MapReduce
│   ├── 📄 capacity-scheduler.xml     # Configuración del scheduler de YARN
│   └── 📄 log4j.properties           # Configuración de logs
│
├── 📂 scripts/                        # Scripts de inicialización
│   ├── 📄 start-hdfs.sh              # Inicia el NameNode
│   ├── 📄 init-datanode.sh           # Inicia los DataNodes
│   ├── 📄 start-yarn.sh              # Inicia el ResourceManager
│   └── 📄 start-nodemanager.sh       # Inicia el NodeManager
│
├── 📂 src/                            # Código fuente
│   └── 📂 prod-cons/                 # Productores y consumidores de Kafka
│       ├── 📂 json/                  # Versión con mensajes JSON
│       │   ├── 📄 producer_json.py
│       │   └── 📄 consumer_json.py
│       └── 📂 plain/                 # Versión con mensajes de texto plano
│           ├── 📄 producer.py
│           └── 📄 consumer.py
│
└── 📂 data/                           # Directorio para datos de prueba
    └── 📄 README.txt
```

## 🛠️ Requisitos  

- **Docker** y **Docker Compose** instalados en el sistema
- **RAM**: Mínimo 6GB libres (recomendado 8GB+)
- **Espacio en disco**: Al menos 10GB libres para imágenes y volúmenes
- **Sistema Operativo**: Windows, Linux o macOS con Docker Desktop

## ⚡ Instalación y Uso  

### 1️⃣ Iniciar el Clúster

```bash
# Construir e iniciar todos los servicios
docker-compose up -d --build

# Ver el estado de los contenedores
docker ps

# Ver logs de un servicio específico
docker-compose logs -f namenode
docker-compose logs -f kafka
```

### 2️⃣ Acceder a las Interfaces Web

Una vez iniciados los servicios, puedes acceder a:

| Servicio | URL | Descripción |
|----------|-----|-------------|
| **NameNode UI** | http://localhost:9870 | Interfaz web de HDFS |
| **YARN ResourceManager** | http://localhost:8088 | Gestión de aplicaciones YARN |
| **NodeManager UI** | http://localhost:8042 | Estado del NodeManager |
| **Kafka UI** | http://localhost:8080 | Administración de Kafka |
| **Kafka Connect** | http://localhost:8083 | API REST de Kafka Connect |

### 3️⃣ Interactuar con los Servicios

**Acceder al NameNode:**
```bash
docker exec -it namenode bash
```

**Acceder a Kafka:**
```bash
docker exec -it kafka bash
```

**Acceder a Kafka Connect:**
```bash
docker exec -it kafka-connect bash
```

<p align="right">(<a href="#readme-top">Volver arriba</a>)</p>

## 📌 Comandos Útiles  

### HDFS (desde el contenedor namenode)

```bash
# Listar archivos en HDFS
hdfs dfs -ls /

# Crear un directorio
hdfs dfs -mkdir -p /user/hadoop/data

# Subir un archivo a HDFS
hdfs dfs -put archivo.txt /user/hadoop/data/

# Descargar un archivo de HDFS
hdfs dfs -get /user/hadoop/data/archivo.txt .

# Ver el contenido de un archivo
hdfs dfs -cat /user/hadoop/data/archivo.txt

# Ver el estado del clúster
hdfs dfsadmin -report

# Verificar la salud del sistema de archivos
hdfs fsck /

# Salir del Safe Mode (si es necesario)
hdfs dfsadmin -safemode leave
```

### Kafka (desde el contenedor kafka)

```bash
# Listar topics
kafka-topics --bootstrap-server localhost:9092 --list

# Crear un topic
kafka-topics --bootstrap-server localhost:9092 --create --topic test --partitions 3 --replication-factor 1

# Describir un topic
kafka-topics --bootstrap-server localhost:9092 --describe --topic test

# Producir mensajes (consola)
kafka-console-producer --bootstrap-server localhost:9092 --topic test

# Consumir mensajes (consola)
kafka-console-consumer --bootstrap-server localhost:9092 --topic test --from-beginning

# Eliminar un topic
kafka-topics --bootstrap-server localhost:9092 --delete --topic test
```

### Docker Compose

```bash
# Iniciar todos los servicios
docker-compose up -d

# Detener todos los servicios
docker-compose down

# Detener y eliminar volúmenes (¡cuidado! elimina datos)
docker-compose down -v

# Ver logs de todos los servicios
docker-compose logs -f

# Reconstruir un servicio específico
docker-compose up -d --build namenode

# Reiniciar un servicio
docker-compose restart kafka
```

<p align="right">(<a href="#readme-top">Volver arriba</a>)</p>


## 🏗️ Arquitectura del Sistema

### Stack Kafka
- **Zookeeper**: Coordinación y gestión de metadatos de Kafka
- **Kafka**: Broker de mensajería para streaming de datos
- **Kafka Connect**: Integración con HDFS mediante conector preinstalado
- **Kafka UI**: Interfaz gráfica para administración y monitoreo

### Stack Hadoop
- **NameNode**: Nodo maestro de HDFS que gestiona los metadatos
- **DataNode1 y DataNode2**: Nodos de almacenamiento distribuido (replicación x2)
- **ResourceManager**: Gestor de recursos de YARN
- **NodeManager**: Ejecutor de tareas en el clúster YARN

### Red y Comunicación
- Todos los servicios comparten la red `cluster_network`
- Comunicación mediante nombres de host (DNS de Docker)
- No se requieren IPs estáticas

## 🎯 Casos de Uso

### 1. Procesamiento Batch con MapReduce
- Almacena datos en HDFS
- Ejecuta jobs MapReduce con YARN
- Analiza grandes volúmenes de datos

### 2. Streaming en Tiempo Real
- Produce mensajes a Kafka
- Consume y procesa streams
- Integra con HDFS mediante Kafka Connect

### 3. Pipeline Híbrido
- Ingesta datos en tiempo real con Kafka
- Almacena en HDFS mediante Kafka Connect
- Procesa con MapReduce para análisis histórico

## 📝 Mejoras Implementadas

✅ **Docker Compose Simplificado**
- Uso de YAML anchors para eliminar duplicación
- Volúmenes Docker nombrados en lugar de bind mounts
- Red bridge simple sin IPs estáticas

✅ **Scripts Optimizados**
- Manejo de errores con `set -e`
- Inicialización segura de directorios
- Separación correcta de responsabilidades

✅ **Configuración Mejorada**
- Comentarios en inglés para mejor comprensión
- Formato consistente en todos los archivos XML
- Valores optimizados para entorno de desarrollo

✅ **Seguridad**
- Montajes de solo lectura para configs y scripts
- Prevención de modificaciones accidentales

## ❓ FAQ  

### **Error: "unexpected end of file" en scripts**
Los scripts pueden tener formato de línea Windows. Conviértelos:
```bash
# Verificar el formato
cat -A scripts/start-hdfs.sh

# Convertir si ves ^M al final de las líneas
sed -i 's/\r$//' scripts/*.sh
```

### **NameNode en Safe Mode**
El NameNode entra en Safe Mode al inicio. Para salir:
```bash
docker exec -it namenode bash
hdfs dfsadmin -safemode leave
```

### **Los DataNodes no se conectan**
Verifica que el NameNode esté completamente inicializado:
```bash
docker-compose logs namenode
hdfs dfsadmin -report
```

### **Kafka no puede conectarse a Zookeeper**
Asegúrate de que Zookeeper esté funcionando:
```bash
docker-compose logs zookeeper
docker exec -it zookeeper bash
echo stat | nc localhost 2181
```

### **Los volúmenes persisten datos entre reinicios**
Sí, los volúmenes Docker nombrados mantienen los datos. Para limpiar:
```bash
# Ver volúmenes
docker volume ls

# Eliminar todo (¡cuidado!)
docker-compose down -v
```

## 📝 Notas Importantes

- ⚠️ Este entorno está configurado para **desarrollo y pruebas**, no para producción
- 🔒 La seguridad está deshabilitada para facilitar el desarrollo
- 📊 El factor de replicación de HDFS es 2 (requiere ambos DataNodes)
- 🔄 Los volúmenes Docker persisten datos entre reinicios
- 🛠️ Puedes escalar añadiendo más DataNodes en `docker-compose.yml`


## 🚀 Próximos Pasos

Después de tener el clúster funcionando, puedes:

1. **Explorar HDFS**: Sube archivos y experimenta con comandos
2. **Ejecutar Jobs MapReduce**: Procesa datos distribuidos
3. **Crear Topics en Kafka**: Implementa productores y consumidores
4. **Configurar Kafka Connect**: Conecta Kafka con HDFS
5. **Desarrollar Aplicaciones**: Usa Python para interactuar con ambos sistemas

## 📖 Referencias  

- [Documentación oficial de Hadoop](https://hadoop.apache.org/docs/stable/)  
- [Documentación oficial de Apache Kafka](https://kafka.apache.org/documentation/)
- [Kafka Connect HDFS Connector](https://docs.confluent.io/kafka-connect-hdfs/current/index.html)
- [Docker Hub - Hadoop Images](https://hub.docker.com/r/apache/hadoop)
- [Docker Hub - Confluent Platform](https://hub.docker.com/u/confluentinc)

## 👥 Contribuciones

¿Encontraste un bug o tienes una sugerencia? ¡Abre un issue o pull request!

## 📄 Licencia

Este proyecto está bajo la licencia MIT. Consulta el archivo `LICENSE` para más detalles.

<p align="right">(<a href="#readme-top">Volver arriba</a>)</p>

---

**Desarrollado para la asignatura Ingeniería de Datos: Big Data**  
*Máster en Ingeniería del Software - Cloud, Datos y Gestión TI*  
*Universidad de Sevilla*