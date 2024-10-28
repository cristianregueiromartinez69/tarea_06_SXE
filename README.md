### TAREA 6 SXE: Utiliza docker para poner en marcha Prestashop

**Índice**
- creación de la carpeta con el archivo docker-compose 
- configurar servicio de mysql
- configurar servicio de docker compose
- arrancar docker compose y comprobar que funciona


## 1. creación de la carpeta con el archivo docker-compose :smile:
```bash
#Creamos una carpeta donde vamos a almacenar el docker compose
mkdir archivo_compose_prestashop

#creamos y nos preparamos para editar el docker-compose
nano archivo_compose_prestashop/docker-compose.yml

```

# 2. configurar servicio de mysql :smile:
```bash
#fragmento de estructura del servicio mysql para prestashop
services:
  db:
    image: mariadb:10.11.2
    volumes:
    - db_data_prestashop:/var/lib/mysql
    restart: no
    environment:
      MYSQL_ROOT_PASSWORD: 123456789
      MYSQL_DATABASE: prestashop
      MYSQL_USER: prestashop_user
      MYSQL_PASSWORD: 123456789
```
## EXPLICACIÓN DE LA ESTRUCTURA :smile:
``` bash
#services:
sirve para declarar los servicios que vamos a instalar en los contenedores, considéralos como un gran conjunto en el cual vamos a ir estableciendo las propiedades de los diferentes servicios
```

``` bash
#db:
nombre que le vamos a dar al servicio
```

``` bash
#image: mariadb:10.11.2
se descargará la imagen de mariadb que le indiquemos
```

``` bash
#volumes:
    #- db_data_prestashop:/var/lib/mysql
Vamos a montar un volumen llamado db_data en donde se guardan las bases de datos de mariadb, esto quiere decir que podremos tener los datos pese a que se corrompa el servicio
```

``` bash
#restart: no
aquí indicamos si queremos que el servicio se reinicie automáticamente cuando falle algo, ponemos que no porque si ponemos always y metemos algo mal en la configuración al arrancarlo, no seremos capaces de pararlo
```

``` bash
#environment
las variables de entorno del servicio, variables de contraseña, user... etc
```

# 3. configurar servicio de docker compose :smile:
```bash
#estructura del fragmento de prestashop
 prestashop:
    depends_on:
      - db
    image: prestashop/prestashop:latest
    ports:
      - "9800:80"
    restart: no
    environment:
      PS_DEV_MODE: 0
      PS_HOST_MODE: 0
      PS_DEMO_MODE: 0
      DB_SERVER: db
      DB_USER: prestashop_user
      DB_PASSWD: 123456789
      DB_PREFIX: ps_
      DB_NAME: prestashop
      PS_LANGUAGE: en
      PS_SHOP_NAME: tienda prueba
      PS_DOMAIN: "localhost:9800"
    networks:
      - prestashop_network

volumes:
  db_data_prestashop:

networks:
    prestashop_network:

```

# EXPLICACIÓN DE LA ESTRUCTURA DE PRESTASHOP :smile:
```bash
#prestashop:
el nombre del servicio
```

```bash
# depends_on:
     # - db
Esto quiere decir que prestashop va a depender de mariadb para funcionar, sin ella, no arranca el servicio
```

```bash
#image: prestashop/prestashop:latest
se va a descargar la ultima imagen de prestashop, indicado mediante el tag latest
```

```bash
#ports:
     # - "9800:80"
vamos a mapear el puerto 9800 con el puerto 80 de la maquina, de manera que al acceder desde fuera, la conexion pasara por el puerto de la maquina virtual y podremos acceder a prestashop
```

```bash
#restart: no
esto es para indicar que hace el servidor en caso de fallo, puse que no por si la instalacion salía mal, pero puedes poner always por ejemplo si quieres que siempre esté activo
```

```bash
#environment:
Aquí puse las variables de entorno, tales como el usuario, nombre de la tienda, contraseña... etc, las podéis consultar aquí:
https://hub.docker.com/r/prestashop/prestashop/
```

```bash
#networks:
      #- prestashop_network
la red en la que va a estar prestashop
```

```bash
#volumes:
  #db_data_prestashop:
aquí declaramos el volumen que va a ser usado por los servicios. Importante declararlo fuera de cualquier servicio para que sea accesible a todos
```

```bash
#networks:
    #prestashop_network:
Mismo ejemplo para la red, declarada fuera de los servicios ya que va a ser usada por los 2 mencionados anteriormente
```
