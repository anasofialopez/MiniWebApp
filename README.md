# Parcial Final 
**Integrantes**
- Ana Sofia Lopez - 2205432
- Juan Sebastian Varona Parra - 2205432

## Prometheus y Node Explorer
Te dejo esto a ti ana la 🐸

## Empaquetamiento Docker
Clonamos el repo de [MiniWebApp](https://github.com/omondragon/MiniWebApp)
Creamos el `webapp/Dockerfile`
```
FROM python:3.6

EXPOSE 5000
ENV FLASK_APP=run.py

WORKDIR /app
COPY . /app

COPY requirements.txt /app
RUN pip install -r requirements.txt

COPY run.py /app

CMD ["python3", "-m", "flask", "run", "--host=0.0.0.0"]
```
Creamos el  `docker-compose.yml` en el directorio raíz. 
```
version: "2"
services:
  app:
    build:
      context: webapp
      dockerfile: Dockerfile
    links:
      - db
    ports:
      - "5000:5000"

  db:
    image: mysql:5.7
    ports:
      - "32000:3306"
    environment:
      MYSQL_ROOT_PASSWORD: root
    volumes:
      - ./init.sql:/docker-entrypoint-initdb.d/init.sql
```
Aquí definimos el Dockerfile con el que vamos a crear la imagen de `app` y también vamos a especificar donde está el archivo `init.sql` con el que populamos la base de datos MySQL.

Ahora creamos el  `webapp/requirements.txt`
```
Flask
mysql-connector
flask-cors
Flask-MySQLdb
Flask-SQLAlchemy
```
Cambiamos el `webapp/config.py`  para que el host sea el link `db` que configuramos en el `docker-compose.yml`
```
class Config:
    MYSQL_HOST = 'db'
    MYSQL_USER = 'root'
    MYSQL_PASSWORD = 'root'
    MYSQL_DB = 'myflaskapp'
    SQLALCHEMY_DATABASE_URI = f'mysql://{MYSQL_USER}:{MYSQL_PASSWORD}@{MYSQL_HOST}:3306/{MYSQL_DB}'
```
Estas son todas las configuraciones necesarias para empaquetar nuestro proyecto en Docker.
Ahora si corremos dentro del directorio raiz del repositorio (donde se ubica el `docker-compose.yml`) el comando `sudo docker compose up -d` debería empezar a crear y correr nuestras imagenes en el background. 

Una vez terminado esto, si corremos `curl http://localhost:5000` debería retornarnos el HTML del `index.html` de la MiniWebApp. 

Y si accedemos desde nuestro navegador a `<ip-servidor>:5000` podremos hacer uso de la interfaz gráfica y debería funcionar correctamente.

## Despliegue en AWS
Para el despliegue debemos tener una instancia de Ubuntu 22.04 funcionando y debemos tener los siguientes permisos habilitados:
PERMISOS

Ahora debemos tener instalado `nginx` en nuestra maquina y también `docker` como en el punto anterior.
Clonamos el código del repositorio del punto anterior y corremos el mismo comando `sudo docker compose up`
Si corremos dentro del shell de la maquina de AWS el comando:  `curl http://localhost:5000` deberíamos recibir el HTML de la MiniWebApp. 
