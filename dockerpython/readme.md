 # Docker+Python
Como prerequisito necesitamos la imagen de python ya que vamos a usar scripts en python.

~~~
docker pull python:3
~~~
Ahora si, para empezar construiremos la imagen quq queremos con el comando:

~~~
docker build -t youtubeimagen:latest .
~~~

Despues crearemos un archivo docker-compose.yml con el que generaremos una la imagen "youtubeimagen" para ejecutar un script, en mi caso llamado "hola.py"

~~~
services:
  python:
    image: youtubeimagen
    volumes:
        - ./app:/usr/src/app
    stdin_open: true
    tty: true
    working_dir: /usr/src/app
    command: python hola.py
~~~

Una vez creado este archivo haremos las carpetas que queremos mapear con la imagen. Como se ve en el archivo docker-compose.yml necesitamos una carpeta llamada app.

~~~
sudo mkdir app
~~~

Lo siguiente que necesitamos es el script que use la librebria pytube, ya que vamos a usar una funcion de esta libreria. El script será el siguiente:

~~~
from pytube import YouTube

yt = YouTube("https://youtu.be/e-tGLbQmXu4")

#Title of video
print("Title: ",yt.title)
#Number of views of video
print("Number of views: ",yt.views)
#Length of the video
print("Length of video: ",yt.length,"seconds")
#Description of video
print("Description: ",yt.description)
#Rating
print("Ratings: ",yt.rating)

yt = YouTube('https://youtu.be/e-tGLbQmXu4')
yt.streams.filter(progressive=True, file_extension='mp4').order_by('resolution').desc().first().download()
~~~

Nos servirá para descargar videos de YouTube y ver los datos del archivo y el video. Pero para usar la libreria de pytube deberemos instalarla en nuestra imagen. Para esto usaremos un archivo llamado Dockerfile, que nos permite personalizar nuestra imagen y estará contenido en la carpeta app.

Arvhivo Dockerfile:

~~~
FROM python:3

WORKDIR /usr/src/app

COPY requirements.txt ./

#RUN pip install pytube

RUN pip install --no-cache-dir -r requirements.txt
~~~

Como se ve en la configuracion del archivo Dockerfile vamos a usr otro archivo llamado requirements.txt, asi que vamos a crearlo y editarlo para añadir la libreria pytube

Archivo requirements.xt

~~~
pytube==12.1.2
PyChromecast
~~~

Una vez listos todos los archivos al hacer docker-compose up se ejecutará el script contenido en la imagen y descargara el video de YouTube que indicamos en el script.

Logs del docker-compose up:
~~~
asir2a@perlanegra17:~/dockerpython/app$ docker-compose up
Recreating dockerpython_python_1 ... done
Attaching to dockerpython_python_1
python_1  | Title:  Skibidi bop + Bloody mary    [TikTok Mashup]  ///   subtitulado
python_1  | Number of views:  320660
python_1  | Length of video:  193 seconds
python_1  | Description:  CANCIÓN: Bloody Mary
python_1  | ARTISTA: Lady Gaga
python_1  | ÁLBUM: Bloody Mary
~~~

Como podemos comprobar funciona perfectamente. Ahora que sabemos que funciona subiremos la imagen al servicio web de docker.

Se necesita tener una cuenta en la web de docker y crear un repositorio para esto, dado que yo ya la tengo solo tengo que usar los siguientes comando para iniciar sesion desde mi terminal, tagear la imagen al repositorio y subir la imagen

~~~
docker login
~~~
~~~
docker tag imagen usuario/repositorio
~~~
~~~
docker push username/imagen:latest
~~~