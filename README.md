Fernández Jeremías
Seminario de Actualización III – Microservicios

Primer trabajo: Contenedores con Apache y MySql
Primero utilizamos el comando para poner a correr el contenedor apache, ya de paso, también guardamos su volumen:
docker run -d -p 8088:80 --name apache-container -v C:\Users\Deelyn_ksks\Desktop\Tareas\Microservicios\"Primer trabajo"\php:/var/www/html php:apache-bullseye
Luego, hay que entrar a la terminal del contenedor apache, y ejecutar los siguientes comandos proporcionados en la tarea:
docker-php-ext-install mysqli
docker-php-ext-enable mysqli
Después, ponemos a correr el contenedor de mysql, y también guardamos su volumen: 
docker run -d --name mysql-container -p 3306:3306 -e MYSQL_ROOT_PASSWORD=password -v C:\Users\Deelyn_ksks\Desktop\Tareas\Microservicios\"Primer trabajo"\mysql:/var/lib/mysql mysql:debian
Abrir la terminal del contenedor MySql, entrar con el usuario y contraseña así:
mysql -u root -p
Y ejecutar las querys proporcionadas:

```sql
-- Crear la base de datos "prueba"
CREATE DATABASE IF NOT EXISTS prueba;

-- Usar la base de datos "prueba"
USE prueba;

-- Crear la tabla "alumnos"
CREATE TABLE IF NOT EXISTS alumnos (
    id INT AUTO_INCREMENT PRIMARY KEY,
    apellidos VARCHAR(200),
    nombres VARCHAR(200),
    dni INT(11)
);

-- Insertar registros en la tabla "alumnos"
INSERT INTO alumnos (apellidos, nombres, dni) VALUES
    ('García', 'Juan', 345678901),
    ('López', 'María', 456789012),
    ('Martínez', 'Carlos', 367890123);

```

Después ejecutamos este comando para traer la ip de nuestro contenedor apache:
docker inspect \ -f '{{range.NetworkSettings.Networks}}{{.IPAddress}}{{end}}' códigodelcontenedorquehiciste
Cuando tenemos la ip, debemos editar el archivo .php proporcionado en la tarea. Debería de quedar así:
	    $servername = "172.17.0.3";
      $username = "root";
      $password = "12345";
      $dbname = "prueba";
Por último, ingresar a: http://localhost:8088
Y si todo sale bien, se va a visualizar la tabla de los alumnos
![image](https://github.com/DeelynKsks/Docker_Documentacion/assets/73413595/aa50f021-a9f2-4221-9c13-13e9c868ca2b)


Segundo trabajo: Balanceador de carga
	Creamos otra carpeta php con el index allí, y otro contenedor apache sin el puerto
docker run -d --name apache-container2 -v C:\Users\Deelyn_ksks\Desktop\Tareas\Microservicios\"Primer trabajo"\php2:/var/www/html php:apache-bullseye
	Instalar nginx con el comando: docker pull nginx:latest
	Lo que hice fue crear una carpeta, con un archivo llamado “default.conf”, en el
	que escribí el siguiente código:
upstream balanceador {
    server 172.17.0.2:80;
    server 172.17.0.4:80;
}
server {
    location / {
        proxy_pass http://balanceador;
    }
}

![image](https://github.com/DeelynKsks/Docker_Documentacion/assets/73413595/13f90a3b-8b4b-4eb4-959f-96a224ab0dd0)


Este archivo, reescribe la configuración del balanceador al crear el contenedor con la imagen de nginx.
Utilicé el siguiente comando para crear el contenedor de nginx:
docker run -d -p 8085:80 --name balanceador -v C:\Users\Deelyn_ksks\Desktop\Tareas\Microservicios\"Primer trabajo"\balanceador:/etc/nginx/conf.d nginx:latest
Edité los respectivos archivos php para diferenciar el contenedor 1 del 2, y por último accedí a: http://localhost:8085 (el puerto 8085 es el del balanceador)
Y los resultados son estos:
![image](https://github.com/DeelynKsks/Docker_Documentacion/assets/73413595/5c56cc98-112d-4122-8a21-6da2149c2688)
![image](https://github.com/DeelynKsks/Docker_Documentacion/assets/73413595/1cf19812-fcb2-4343-8055-e79b6eaf574e)

