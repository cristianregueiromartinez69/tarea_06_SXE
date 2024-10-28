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