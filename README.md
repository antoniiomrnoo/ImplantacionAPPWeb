# ImplantacionAPPWeb
# **DIAGRAMA MÁQUINAS VPC EN AWS**

![image](https://github.com/user-attachments/assets/357645d9-91e2-46ab-986f-ef501d62ce10)



Este es el diagrama que representa como he creado mis máquinas dentro de mi VPC, con sus subredes y demás. Una vez hecho esto ya podemos migrar la base de datos.


# **Migración BD a PostgreSQL**

Primero vamos a instalar postgres con este script:

![image](https://github.com/user-attachments/assets/f9487794-d1e3-48f2-833c-df2b461cf89d)


Una vez hecho esto nos conectamos a la BD:

- sudo -i -u postgres
- psql -d nombre_base_datos -U nombre_usuario


Para conectarte desde una máquina remota, tendremos que configurar PostgreSQL para acceptar dicha conexión remota.
Abre el archivo de configuración :

- postgresql.conf:

- sudo nano /etc/postgresql/$(ls /etc/postgresql)/main/postgresql.conf

Busca y edita la línea 'listen_addresses' para que acepte conexiones en todas las interfaces (o una IP específica):

- listen_addresses = '*'

Asegúrate de que en  'pg_hba.conf' estén configuradas las reglas para conexiones remotas. Ya lo hicimos en el script, pero si necesitas permitir acceso a otros hosts, añade algo como:

- host    nombre_base_datos    nombre_usuario    IPapliacion/32    md5

Reinicia PostgreSQL:

- sudo systemctl restart postgresql

Ahora para pasar nuestra BD de django a postgreSQL tenemos que instalar ciertos paquetes:

- sudo apt update

- sudo apt install libpq-dev

- pip install psycopg2

Ahora necesitamos exportar los datos a un archivo JSON:

- python3 manage.py dumpdata > data.json

![image](https://github.com/user-attachments/assets/ba801bad-d27a-446c-961e-a1a1654998f0)

Aplicamos las migraciones en PostgreSQL:

- python3 manage.py makemigrations

- python3 manage.py migrate

Importamos los datos antiguos

- python3 manage.py loaddata data.json

Y de esta forma ya hemos terminado.

![image](https://github.com/user-attachments/assets/2c7e04d6-cf1f-4e45-b94e-93aff4570d37)




# **DOCUMENTACIÓN DE WAGTAIL**

Para implementar CMS (Wagtail) a nuestra aplicación, debemos de añadir lo siguiente a nuestro 'settings.py':

![image](https://github.com/user-attachments/assets/8921d2e4-2a1a-460f-b121-b63b211da260)

![image](https://github.com/user-attachments/assets/0017f0b8-4e08-4afd-ae27-1152e9fcb850)

![image](https://github.com/user-attachments/assets/fdc1cc19-447a-4e63-ac2b-886c79b6beb9)

![image](https://github.com/user-attachments/assets/17749db9-b2dd-415c-aaab-6808d5dfb6bf)

Una vez hecho esto, cuando nos metemos a nuestra aplicación web y buscamos el barra de navegación las urls que hemos puesto:

- /cms

![image](https://github.com/user-attachments/assets/69e9a374-aac5-4f34-a61c-599d26d3d16a)

![image](https://github.com/user-attachments/assets/00e1a338-8ef8-495e-aba0-70cc7b2797a0)

- /blog

![image](https://github.com/user-attachments/assets/973fb0a0-e509-48e8-9fc5-64886dc92471)

Esto es la implementación de Wagtail.



# **Autenticación LDAP en Django**

Primero creamos nuestro contenedor:

![image](https://github.com/user-attachments/assets/0230de13-a439-4f31-8cb4-c3c342c3d1c8)

Instalamos java para poder abrir el Apache Directory Studio:

- sudo apt-get install openjdk-11-jdk

Instalamos el Apache Directory Studio:

https://directory.apache.org/studio/download/download-linux.html

Instalamos docker y docker compose:

- sudo apt-get update

- sudo apt-get install ca-certificates curl

- sudo install -m 0755 -d /etc/apt/keyrings

- sudo curl -fsSL https://download.docker.com/linux/ubuntu/gpg -o /etc/apt/keyrings/docker.asc

- sudo chmod a+r /etc/apt/keyrings/docker.asc

- echo \
  "deb [arch=$(dpkg --print-architecture) signed-by=/etc/apt/keyrings/docker.asc] https://download.docker.com/linux/ubuntu \
  $(. /etc/os-release && echo "$VERSION_CODENAME") stable" | \
  sudo tee /etc/apt/sources.list.d/docker.list > /dev/null

- sudo apt-get update

- sudo apt-get install docker-ce docker-ce-cli containerd.io docker-buildx-plugin docker-compose-plugin

- sudo apt install docker-compose -y

Siguiendo todos estos comandos en orden ya podríamos usar docker.

Ahora vamos a modificar nuestro 'settings.py' para empezar las configuraciones de LDAP:

![image](https://github.com/user-attachments/assets/c8026d36-00a9-422c-ae8c-34d2737be554)

![image](https://github.com/user-attachments/assets/1111e71e-4d9a-49ff-8f14-33c95512acee)

En 'apps.py':

![image](https://github.com/user-attachments/assets/f3d3c57e-4480-4bd8-a460-767d282c363e)

En 'signals.py':

![image](https://github.com/user-attachments/assets/39ed361a-50cc-4a1a-b8c3-38f25af7d1bb)

Ahora en Apache Directory Studio debemos tener lo siguiente:

![image](https://github.com/user-attachments/assets/5b611976-3216-43f0-af1c-c7c4a69a5983)

![image](https://github.com/user-attachments/assets/c03631a9-8b90-45db-afac-6d7aca0da16c)

Una vez hecho esto vamos a probar el funcionamiento del correo electrónico:

![image](https://github.com/user-attachments/assets/46279e56-251b-449e-839c-7ca88410d69f)



# **MEJORA DE LA APLICACIÓN WEB**

**- OpenID-------------------------------------------------------------------------------------------------------------------------------**
  
Para implementar OpenID debemos seguir los siguientes pasos:

- pip install django-allauth

Ahora vamos a crear nuestro proyecto en Google para las claves 'cliente' y 'secret key' que usaremos posteriormente en el 'settings.py':

Una vez dentro debemos crear un cliente con esta configuración:

![image](https://github.com/user-attachments/assets/9177ce77-1ad1-4861-af50-0d186824effc)

![image](https://github.com/user-attachments/assets/8656c9bf-bbb8-4da1-8049-597de05020fd)

Ahora en el 'settings .py':

![image](https://github.com/user-attachments/assets/165a7e3a-a1e4-4949-9ba3-8c1696079e9f)

![image](https://github.com/user-attachments/assets/c36a9a8d-e9ae-4e53-94ac-a57470f416a6)

![image](https://github.com/user-attachments/assets/0f8c695c-1a12-4ff3-9c50-97270d17cb99)

![image](https://github.com/user-attachments/assets/5a67b221-b5d7-46d1-ad43-c67560d575a0)

Ahora al 'urls.py':

![image](https://github.com/user-attachments/assets/7434ad22-4751-4962-90f9-d5e610b16522)

Modificamos el template de 'login.html':

![image](https://github.com/user-attachments/assets/896046af-552e-4da4-8aaa-db36629a5f01)

Y le damos CSS al gusto. Una vez hecho todo esto debemos hacer lo siguiente:

- python3 manage.py migrate

- python3 manage.py runserver

Accedemos a nuestra app y al iniciar sesión:

![image](https://github.com/user-attachments/assets/95545902-c988-4ee0-b2e7-d9cec569d73a)

![image](https://github.com/user-attachments/assets/43b3421e-cdc1-4b62-864d-10b992448194)

![image](https://github.com/user-attachments/assets/86cf3436-a526-42d6-9b2a-3259f68e1dcf)

**- Posicionamiento web (SEO)---------------------------------------------------------------------------------------------------**

Para el posicionamiento web (SEO) debemos seguir los siguientes pasos:

![image](https://github.com/user-attachments/assets/04d8b5f9-c729-413a-8345-b43b0696e295)

![image](https://github.com/user-attachments/assets/a1f2be54-35f5-4880-8a19-a4826841b5ac)

Añadimos esta etiqueta META a nuestro 'base.html'.

![image](https://github.com/user-attachments/assets/848f3cff-c023-42a7-a606-add1c797eabd)

![image](https://github.com/user-attachments/assets/9e0181f9-01a3-4879-a7db-8a7546ee8dcd)

Tenemos que esperar y nos dará un análisis.

**- Analíticas web de uso de Matomo [docker] como plataforma------------------------------------------------------**

Accedemos a la web proporcianada en la página:

![image](https://github.com/user-attachments/assets/9c7cc320-7c7b-4ae5-bacf-aac79dec6c96)

Seleccionamos ' Try it free with email'.

![image](https://github.com/user-attachments/assets/a1a6d39a-49a1-44cd-9854-52053a7c1170)

![image](https://github.com/user-attachments/assets/c3fe3d8e-e9c9-4204-80e2-7f0f70baea57)

![image](https://github.com/user-attachments/assets/a250df73-6918-4a53-80b6-1df8df30aba5)

![image](https://github.com/user-attachments/assets/2fd1dff2-40fe-486f-ad7c-348c03d72077)

![image](https://github.com/user-attachments/assets/0a0a46e7-7042-43f6-87bf-ba9978e2544c)

**MATOMO EN DOCKER-------------------------------------------------------------------------------------------**

![image](https://github.com/user-attachments/assets/51816b33-c8c6-475a-91bc-5cf280000949)

![image](https://github.com/user-attachments/assets/5b899027-52bd-4358-8b32-bf7afe16167c)

- /var/www/matomo/matomo/config/config.ini.php

![image](https://github.com/user-attachments/assets/4f23e08a-eb5f-4efa-b8f4-38454020703c)

- /etc/nginx/sites-available/default

![image](https://github.com/user-attachments/assets/daa65325-ea1d-45b5-a11e-e443ca5dba36)

![image](https://github.com/user-attachments/assets/2b564d9e-0d7c-4877-884f-9fd3346ecf81)

![image](https://github.com/user-attachments/assets/72d02ed3-dbea-4dd1-946e-44ac5c31c186)

![image](https://github.com/user-attachments/assets/4174d8d1-3b04-405d-bbcd-2b051de1b440)

![image](https://github.com/user-attachments/assets/03153e07-d9bb-4458-a5af-3da894ca41ee)

![image](https://github.com/user-attachments/assets/3b681f63-3e94-45a2-9289-6f5e095bf702)

![image](https://github.com/user-attachments/assets/3b5c42c6-05bc-4131-a681-5044e1dc2c90)

![image](https://github.com/user-attachments/assets/9de59ea3-a34f-44fd-8893-502426b30d75)

![image](https://github.com/user-attachments/assets/579c5e3b-a3a3-4b6a-af73-394eb85db3ec)

![image](https://github.com/user-attachments/assets/8a90268c-db34-4e7a-966e-ffb53acae9fb)

![image](https://github.com/user-attachments/assets/c3daa134-ab84-429b-bbe9-e7487dcd5a61)

![image](https://github.com/user-attachments/assets/004e4e58-a572-404b-98c9-8ff8311c5853)

![image](https://github.com/user-attachments/assets/65791701-e8f4-44b4-b5f0-4ffbb2461471)

