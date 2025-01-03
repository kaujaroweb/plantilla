## OBJETIVO : Explicar bien la base de la plantilla


## 1.PRIMER PASO
- Crear carpeta para el proyecto ( en este caso "plantilla")
- Dentro de ese directorio hacer 
"django startproyect proyecto" 
- Cambiar el nombre de la aplicacion que se crea automaticamente a "app". Esta sera la aplicación central

## 2.SEGUNDO PASO
- Estando dentro de "plantilla", crear un .venv usando el comando 
"python -m venv .venv"
- Para activarlo:
C:\Users\GRA\Desktop\PROJECTS\plantilla>.venv\Scripts\activate

## 3.CREAR UN REQUIREMENTS.TXT
- Ahora estando dentro de "proyecto" crear un archivo que se llame "requirements.txt" con las siguientes cosas:
django
gunicorn
requests
django-dotenv
pyscopg2-binary
django-storages
boto3

## 4.CREAR UN .ENV
- A la altura de "proyecto", crear un archivo ".env"
Este sirve para guardar todas las variables que no se van a subir a github

Copiar esto:

DEBUG=0
DJANGO_SUPERUSER_USERNAME=admin
DJANGO_SUPERUSER_PASSWORD=guria
DJANGO_SUPERUSER_EMAIL=kaujaroweb@gmail.com
DJANGO_SECRET_KEY= luego_la_pongo

POSTGRES_READY= 1
POSTGRES_DB = dockerdc
POSTGRES_PASSWORD= guria_luego
POSTGRES_USER=guria
POSTGRES_HOST=postgres_db
POSTGRES_PORT=5432

REDIS_HOST= redis_db
REDIS_PORT=6380

## 5.CREAR REPOSITORIO DE GIT
- Estando en "plantilla" poner estos comandos:
git init
git add .
git commit m "Commit inicial"
git remote add origin git@github.com:kaujaroweb/plantilla.git
git push -u origin master

## 6.EMPEZAR A USAR EL .ENV
- Cambiar el wsgi.py de "app" para que entienda cual es su parent


import os
import pathlib
from dotenv import load_dotenv  # Corrected import for python-dotenv
from django.core.wsgi import get_wsgi_application

CURRENT_DIR = pathlib.Path(__file__).resolve().parent
BASE_DIR = CURRENT_DIR.parent
ENV_FILE_PATH = BASE_DIR / ".env"

    # Load environment variables from the .env file
load_dotenv(dotenv_path=str(ENV_FILE_PATH))

os.environ.setdefault('DJANGO_SETTINGS_MODULE', 'app.settings')

application = get_wsgi_application()


- Cambiar el "manage.py" :

import os
import sys
from dotenv import load_dotenv  # Corrected import for python-dotenv


def main():
    """Run administrative tasks."""
    os.environ.setdefault('DJANGO_SETTINGS_MODULE', 'app.settings')
    try:
        from django.core.management import execute_from_command_line
    except ImportError as exc:
        raise ImportError(
            "Couldn't import Django. Are you sure it's installed and "
            "available on your PYTHONPATH environment variable? Did you "
            "forget to activate a virtual environment?"
        ) from exc
    execute_from_command_line(sys.argv)


if __name__ == '__main__':
    # Load environment variables from .env file
    load_dotenv()
    main()


## 7.EMPEZAR A CAMBIAR "settings.py" DE "app" para poder tener la base de datos POSTGRES SQL. ( https://www.youtube.com/watch?v=NAOsLaB6Lfc&t=6293s Min 42)

- Primero importar os al principio del todo de settings.py
import os
- Cambiar la secret key de django:

- Despues poner todo esto debajo de la base de datos por defecto

DB_USERNAME = os.environ.get("POSTGRES_USER")
DB_PASSWORD = os.environ.get("POSTGRES_PASSWORD")
DB_DATABASE = os.environ.get("POSTGRES_DB")
DB_HOST = os.environ.get("POSTGRES_HOST")
DB_PORT = os.environ.get("POSTGRES_PORT")
DB_AVIAL = all([
    DB_USERNAME,
    DB_PASSWORD,
    DB_DATABASE,
    DB_HOST,
    DB_PORT
])


POSTGRES_READY= str(os.environ.get('POSTGRES_READY')) == "1"



if DB_AVIAL and POSTGRES_READY and True==False:
    DATABASES = {
        "default": {
            "ENGINE": "django.db.backends.postgresql",
            "NAME":  DB_DATABASE,
            "USER": DB_USERNAME,
            "PASSWORD": DB_PASSWORD,
            "HOST": DB_HOST,
            "PORT":  DB_PORT,
        }
    }



