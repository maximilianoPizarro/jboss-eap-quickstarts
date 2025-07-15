# sugpa sample

## **Guía de Instalación de JBoss EAP 7.4 y Uso de MTA**

Esta guía detalla los pasos para instalar JBoss Enterprise Application Platform (EAP) 7.4 utilizando su instalador gráfico, y cómo configurar y utilizar el Migration Toolkit for Applications (MTA) para analizar tus aplicaciones.

## **0\. Prerrequisitos**

Antes de comenzar, asegúrate de tener los siguientes componentes instalados en tu sistema:

- **Java Development Kit (JDK):**
  - Versión mínima recomendada: OpenJDK 21.0.7
  - Puedes verificar tu versión con:  
        java --version  

- **Apache Maven:**
  - Versión mínima recomendada: Apache Maven 3.9.10
  - Puedes verificar tu versión con:  
        mvn --version  

- **Podman:**
  - Necesario para la gestión de contenedores si tu aplicación los utiliza.
  - Asegúrate de que podman-compose también esté disponible si usas archivos compose.

## **1\. Instalación de JBoss EAP 7.4 (con instalador)**

1. **Descarga el instalador:** Obtén el instalador de JBoss EAP 7.4 desde el Portal del Cliente de Red Hat. El archivo típicamente tendrá un nombre similar a jboss-eap-7.4.0-installer.jar.
    - Enlace de descarga: [Red Hat Developers - JBoss EAP](https://developers.redhat.com/products/eap/download#all-download)
2. **Ejecuta el instalador:** Abre una terminal y navega hasta el directorio donde descargaste el archivo JAR. Ejecuta el instalador con el siguiente comando:  
    java -jar jboss-eap-7.4.0-installer.jar  
    <br/>Sigue las instrucciones del asistente gráfico. Durante la instalación, se te pedirá que selecciones el directorio de instalación. Se recomienda usar una ruta como /home/tu_usuario/EAP-7.4.0 o similar.

## **2\. Configuración de la Variable de Entorno JBOSS_HOME**

Después de la instalación, es crucial establecer la variable de entorno JBOSS_HOME para que apunte al directorio raíz de tu instalación de JBoss EAP.

export JBOSS_HOME=/home/maximilianopizarro/EAP-7.4.0  

**Nota:** Puedes añadir esta línea a tu archivo ~/.bashrc, ~/.zshrc o ~/.profile para que la variable se establezca automáticamente cada vez que abras una nueva terminal. Después de añadirla, ejecuta source ~/.bashrc (o el archivo correspondiente) para aplicar los cambios.

## **3\. Iniciar JBoss EAP en Modo Standalone**

Para iniciar el servidor JBoss EAP en modo standalone, utiliza el script standalone.sh ubicado en el directorio bin de tu instalación.

$JBOSS_HOME/bin/standalone.sh  

## **4\. Configuración de la Variable de Entorno MTA_HOME**

Si ya tienes el Migration Toolkit for Applications (MTA) CLI descargado, establece la variable de entorno MTA_HOME para apuntar a su directorio raíz.

- Enlace de descarga: [Red Hat Developers - Migration Toolkit for Applications (MTA)](https://developers.redhat.com/products/mta/download)

export MTA_HOME=/home/maximilianopizarro/Documentos/mta-cli  

**Nota:** Al igual que JBOSS_HOME, considera añadir esta línea a tu archivo de configuración de shell.

## **5\. Ejecutar Análisis con MTA**

Para analizar tu aplicación con MTA, utiliza el comando analyze del CLI de MTA. Asegúrate de ajustar las rutas de input y output según tu proyecto.

$MTA_HOME/bin/mta-cli analyze --input /home/maximilianopizarro/sugpa --output /home/maximilianopizarro/.mta/tooling/vscode/sxljbkkcn-bos5b5vw5 --mode source-only --overwrite --provider java --enable-default-rulesets=true  

- \--input: Ruta a tu proyecto de aplicación (código fuente o binario).
- \--output: Directorio donde MTA generará los informes de análisis.
- \--mode source-only: Analiza solo el código fuente.
- \--overwrite: Sobrescribe los resultados anteriores en el directorio de salida.
- \--provider java: Especifica que el proveedor es Java.
- \--enable-default-rulesets=true: Habilita los conjuntos de reglas predeterminados.

## **6\. Conectarse al JBoss CLI**

Una vez que JBoss EAP esté en ejecución, puedes conectarte a su interfaz de línea de comandos (CLI) para administrar el servidor.

$JBOSS_HOME/bin/jboss-cli.sh --connect  

Esto te conectará al servidor de administración local.

## **7\. Empaquetar la Aplicación (Maven)**

Si tu aplicación es un proyecto Maven, empaquétala en un archivo WAR (o EAR, JAR, etc.) usando el siguiente comando:

mvn package  

Esto creará el archivo sugpa.war (o el nombre de tu artefacto) en el directorio target/.

## **8\. Desplegar la Aplicación**

Para desplegar la aplicación empaquetada en JBoss EAP, utiliza el comando deploy dentro del JBoss CLI (asegúrate de estar conectado).

deploy ./target/sugpa.war  

## **9\. Gestión de Contenedores con Podman Compose**

Si tu proyecto utiliza Podman para la orquestación de contenedores (por ejemplo, para bases de datos o servicios adicionales), aquí tienes comandos útiles:

- **Levantar servicios en segundo plano:**  
    podman-compose up -d  

- **Levantar servicios y reconstruir imágenes (si hay cambios en Dockerfiles):**  
    podman-compose up -d --build  

## **10\. Conexión a la Base de Datos MySQL**

Si tu aplicación interactúa con una base de datos MySQL, puedes conectarte a ella desde la terminal usando el cliente mysql.

mysql -h localhost -u agente -pagente sugpa  

- \-h localhost: Host de la base de datos (puede ser una IP o nombre de host).
- \-u agente: Nombre de usuario de la base de datos.
- \-p agente: Contraseña del usuario (se recomienda no poner la contraseña directamente en el comando por seguridad, sino que el sistema te la pida).
- sugpa: Nombre de la base de datos a la que te quieres conectar.