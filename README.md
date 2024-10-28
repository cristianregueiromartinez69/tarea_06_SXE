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
se va a descargar la última imagen de prestashop, indicado mediante el tag latest
```

```bash
#ports:
     # - "9800:80"
vamos a mapear el puerto 9800 con el puerto 80 de la maquina, de manera que al acceder desde fuera, la conexión pasará por el puerto de la máquina virtual y podremos acceder a prestashop
```

```bash
#restart: no
esto es para indicar que hace el servidor en caso de fallo, puse que no por si la instalación salía mal, pero puedes poner always por ejemplo si quieres que siempre esté activo
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

# 4.  arrancar prestashop y comprobar que funciona

```bash
#Nos posicionamos dentro de la carpeta del docker-compose.yml y ejecutamos
sudo docker compose up -d
```
Si todo ha ido bien, debería de salir algo así :relaxed:
![todo ok maquina](https://github.com/user-attachments/assets/ec706457-07bf-4616-aecd-48f44a263691)


```bash
#acceder a prestashop desde el navegador
http://192.168.1.46:9800
```
Debería de salir esto, seleccionamos idioma y seguimos :relaxed:
![instalacionPrestashop](https://github.com/user-attachments/assets/07f78a69-95ec-4e53-8a85-46d6d75de87d)


```bash
#Aceptamos acuerdos de licencia
```
![instalacionPrestashop2](https://github.com/user-attachments/assets/7d569cd6-b8e2-4477-bd4b-a56b03654be2)


```bash
#introducción de credenciales, nombre de tienda, correo, contraseña... etc
```
![instalacionPrestashop3](https://github.com/user-attachments/assets/c24348c2-3c51-4de9-985f-0f772f11ffa9)


```bash
#instalamos módulos o no, según queramos personalizar nuestra tienda
```
![instalacionPrestashop4](https://github.com/user-attachments/assets/a7a0a301-2f22-41a7-85f0-6686827f3802)



```bash
#establecemos la conexión con la base de datos. IMPORTANTE
dirección de servidor de la base de datos: mismo nombre que le pusisteis en el docker-compose. El puerto podeís dejar el de por defecto
#Resto de credenciales
las mismas que pusisteis en el docker-compose, si no dará error, a no ser que creeis una desde cero otra vez
```
Si todo ha ido bien, saldrá esto :smile:

![instalacionPrestashop7](https://github.com/user-attachments/assets/a0cf58d3-eccf-4bff-a943-92c13b01ca77)


```bash
#Esperamos a que se instale el servicio
```
![instalacionPrestashop8](https://github.com/user-attachments/assets/dbb83aff-ff45-4346-8cf0-b2092933f815)


```bash
#Una vez ha acabado, aparecerá esto
```
![instalacionPrestashop9](https://github.com/user-attachments/assets/84fce41a-9ea9-45f7-9fd6-74403b590a58)


## IMPORTANTE :scream:
**No hemos acabado todo, queda algo muy importante, ya que si no no nos dejan acceder a la tienda**

```bash
#eliminar la carpeta install donde está el contenedor ejecutándose de prestashop
docker exec -it <nombre_o_id_del_contenedor_prestashop> rm -rf /var/www/html/install
```


```bash
#renombrar por seguridad la carpeta admin de dentro del contenedor donde está ejecutandose prestashop
docker exec -it <nombre_o_id_del_contenedor_prestashop> mv /var/www/html/admin /var/www/html/admin896sjdin1230jdndi
```


```bash
#Una vez hecho esto, introducimos esto
http://192.168.1.46/admin896sjdin1230jdndi

#Como veis, accedemos con el mismo admin el cual cambiamos en la carpeta de dentro del contenedor donde tenemos prestashop
```
Debería de salir esto :relaxed:
![instalacionPrestashop11](https://github.com/user-attachments/assets/2e662148-0a85-44a8-a0db-0e184af2a9b1)


```bash
#introducimos el correo y contraseña que pusimos en la instalación y accedemos a la tienda
```
![instalacionPrestashop12](https://github.com/user-attachments/assets/88082fff-00f6-4382-88bd-9722fa35aaaa)




### SI HAS SEGUIDO LA GUÍA AL PIE DE LA LETRA DEBERÍA DE HABER FUNCIONADO TODO, ASÍ QUE ENHORABUENA, YA HAS INSTALADO PRESTASHOP, AHORA PUEDES INTERACTURAR COMO VEAS :heart_eyes:
