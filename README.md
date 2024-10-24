# Practica-daw-1.2

Este es una continuación de la practica 1.1, para opener toda la información te recomido que le eches primero un vistazo a la [documentación de este](../Practica-daw-1.1/README.md)

## Despliega de una aplicación sencilla

En primer lugar crearemos en la carpeta ``scrips`` el archivo ``deploy.sh``

En este realizaremos el despliega de este, comenzaremos con la misma estructura que el ejercicio anterior

``` sh
#!/bin/bash

set -ex

source .env
```

Realizaremos el despliegue mediante GitHub, para esto es necesario eliminar en primer lugar si hay algún repositorio ya clonado, este se encuentra en la carpeta ``/tmp/iaw-practica-lamp``, esto lo realizaremos con el siguiente comando

``` sh
rm -rf /tmp/iaw-practica-lamp
```

A continuación clonaremos un repositorio para realizar el despliega en la ruta donde anteriormente emos eliminado los repositorios anteriores

``` sh
git clone https://github.com/josejuansanchez/iaw-practica-lamp.git /tmp/iaw-practica-lamp
```

Para poder mostrar el contenido de este en el navegador debemos llevar todo el contenido a la carpeta ``/var/www/html``

``` sh
mv /tmp/iaw-practica-lamp/src/* /var/www/html
```

Una vez aquí crearemos una base de datos para esta aplicación con MySQL

``` sh
mysql -u root <<< "DROP DATABASE IF EXISTS $DB_NAME;"
mysql -u root <<< "CREATE DATABASE $DB_NAME;"
```

Ademas debemos de añadir en nuestro archivo ``.env`` la variable de ``DB_NAME`` para poner el nombre de nuestra base de datos, en mi caso le llamare ``db_name``

Crearemos un usuario y lo asociaremos a esta base de datos para no asociar el usuario root a esta, asi en caso de robo de información solo cogerán las que estén asociadas a este usuario y no todas las que tengamos

``` sh
mysql -u root <<< "DROP USER IF EXISTS '$DB_USER'@'localhost';"
mysql -u root <<< "CREATE USER '$DB_USER'@'localhost' IDENTIFIED BY '$DB_PASSWORD';"
mysql -u root <<< "GRANT ALL PRIVILEGES ON $DB_NAME.* TO '$DB_USER'@'localhost';"
```

Ademas añadiremos a el archivo ``.env`` los siguientes datos

``` sh
DB_USER=user
DB_PASSWORD=password
DB_NAME=db_name
```

A continuación modificaremos la conexión a la base de datos de la aplicación en el config.php

``` sh
sed -i "s/database_name_here/$DB_NAME/" /var/www/html/config.php
sed -i "s/username_here/$DB_USER/" /var/www/html/config.php
sed -i "s/password_here/$DB_PASSWORD/" /var/www/html/config.php
```

Modificaremos el script de creación de la base de datos

``` sh
sed -i "s/lamp_db/$DB_NAME/" /tmp/iaw-practica-lamp/db/database.sql
```

Para finalizar ejecutaremos el script de creación de la base de datos


``` sh
mysql -u root $DB_NAME < /tmp/iaw-practica-lamp/db/database.sql
```

Una vez hecho todo esto tendremos desplegada una aplicación básica que podremos consultar con la ip de nuestra instancia