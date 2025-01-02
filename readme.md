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



