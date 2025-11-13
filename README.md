Despliegue de servidores de transferencia de archivos mediante tecnologías de virtualización en la nube y en contenedores

Crea un directorio, por ejemplo FTP que tenga el siguiente docker-compose.yml:

version: '3.8'

services:
  ftp:
    image: stilliard/pure-ftpd
    container_name: ftp
    restart: unless-stopped

    # ⚠️ No usar puertos 20/21 (requieren root)
    ports:
      - "2121:21"                # puerto de control FTP
      - "30000-30009:30000-30009" # puertos pasivos

    environment:
      PUBLICHOST: "172.20.209.155"  # ⚠️ IP de la máquina del alumno
      FTP_USER_NAME: alumno
      FTP_USER_PASS: 1234
      FTP_USER_HOME: /home/ftpusers/alumno
      ADDED_FLAGS: "-d"             # habilita logs en consola

    volumes:
      - ./ftpdata:/home/ftpusers
Crear el directorio y permisos (solo una vez):

mkdir -p ~/ftp/ftpdata
cd ~/ftp
Levantar el contenedor:

docker compose up -d
Verificar que está en ejecución:

docker ps
Debería mostrar algo como:

ftp  ...  Up ...  0.0.0.0:2121->21/tcp, 30000-30009/tcp
Probar la conexión desde otro alumno (o desde su propia máquina):

ftp 172.20.209.155 2121
Usuario: alumno
Contraseña: 1234

2️⃣ Dónde mirar los ficheros

a) Dentro del contenedor
Puedes entrar al contenedor y ver la carpeta FTP:

docker exec -it ftp bash
cd /home/ftpusers
ls -l
Aquí verás los archivos que suben los clientes FTP.
Cualquier cambio aquí se refleja inmediatamente en el host (./ftpdata).

b) En el host
 En su propia máquina, pueden mirar directamente:

cd ~/ftp/ftpdata
ls -l
Los mismos archivos que subieron vía FTP aparecerán aquí.
Pueden añadir archivos manualmente aquí, y el contenedor los verá también.

