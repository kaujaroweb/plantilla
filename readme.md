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

## 8.PREPARAR STATIC FILES Y TEMPLATES PARA PREPARAR EL INSTALAR TAILWIND(PARA DESPUES PONERLO EN DOCKER TAMBIEN)
- Antes de nada crear un folder llamado "static" en proyecto (A la altura de manage.py) y este va a ser con el que va a funcionar todo. Depues va a haber otro folder fuera de esto que va a servir para cuando estemos en produccion y usemos "python manage.py collectstatic", cuando se ejecuta ese comando, se copia lo que tenemos en el "static" a esa otra carpeta. En este caso voy a llamar a esta carpeta de fuera "local-cdn/static" y esta a la altura de "proyecto" (fuera de el logicamente)
- Primero configurar las static files en settings.py
- Debajo de "STATIC_URL" hay que poner:
STATICFILES_DIRS = [BASE_DIR/ "static"]
STATIC_ROOT = BASE_DIR.parent / "local-cdn" / "static" # esto esta fuera de django y se usaria para produccion supuestamente
- Luego hay que ir a urls.py (de la app principal, en este caso "app"), y cambiarlo a esto:
import settings
from django.contrib import admin
from django.urls import path

urlpatterns = [
    path('admin/', admin.site.urls),
]

if settings.DEBUG:
    from django.conf.urls.static import static
    urlpatterns += static(settings.STATIC_URL,  document_root = settings.STATIC_ROOT)
- Para gestionar los templates hay que añadir "templates" a la parte de templates en "settings.py" en "DIRS"
- Ahora en templates crear un base.html y un carpeta llamada "pages" para tener algo de html como ejemplo
- Añadir un views.py a "app"


## 9.INSTALAR TAILWIND A DJANGO
- Lo primero de todo es que el ordenador en el que se ejecuta esto tiene que tener "npm"
- Despues runnear este commando exacto fuera de "proyecto" es decir, a la misma altura que este
- "npm install -D tailwindcss --> esto crea una carpeta llamada "node_modules"
- Ahora runear "npx tailwindcss init" --> Esto crea el archivo tailwind.config.js
- Despues de eso crear dentro de "static" (el de dentro de "proyecto") un folder que se llame "css" con un "input.css" y un "output.css"
- ahora en el "package.json" cambiarlo a esto: 
{
  "scripts":{
    "dev": "tailwindcss --i proyecto/static/css/input.css --o proyecto/static/css/output.css --watch"
  },
  "dependencies": {
    "tailwindcss": "^3.4.15"
  }
}
- Cambiar dentro de "input.css" poner esto: 
@tailwind base;
@tailwind components;
@tailwind utilities;
- Despues para runear esto y que se cambie ejecutar este comando (luego habra que ponerlo en el dockerfile)--> "npm run dev" (desde fuera de proyecto, a la misma altura que este)
- Casi acabando, ahora cada vez que se ponga codigo de tailwind en algun sitio, con el comando anterior se actualiza. Lo unico que falta es cambiar el "tailwind.config.json" para que detecte los archivos que tiene que cambiar, lo que hago con esto es solamente que dentro de proyecto, en cualquier folder, que cualquier archivo html o js lo detecte.
hay que poner esto dentro de tailwind.config.json "content" --> "./proyecto/**/*/{html,js}"

- Si no funciona el comando "npx run dev", usar este --> 
- Estando en "proyecto" --> "npx tailwindcss -i static/css/input.css -o static/css/output.css --watch" 



## 10.EMPEZAR CON DOCKER
- en la carpeta "proyecto" crear un .dockerignore y copiar lo mismo que habia en el gitignore
- ahora a la altura de proyecto vamos a crear un dockerfile.
### Antes de emprezar con docker: 

En Docker se hace un container con el proyecto entero, se utiliza una imagen de python, tambien tenemos que crear 

