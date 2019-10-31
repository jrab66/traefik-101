##Taller Traefik


## docker 

traefik (SSL Lets encrypt) container + docker-compose deploys

Crear archivo ac


instalar docker y docker compose en el server
```
sudo apt-get install docker-io && apt install docker-compose
```

instalar dependencia para usar htpasswd para crear password encryptado para config de traefik
```
sudo apt-get install apache2-utils
```


crear password encriptado 
```
htpasswd -nb admin "ULTRASEGUROPASSWORD"
```
sustituir el campo "admin:admin00" en el archivo docker/traefik.toml por el output del comando anterior por ejemplo
```
admin:xxxxxxxxxxxxxxxxxxxxxxxxxxxxxxxx
```
entrar a la carpeta docker y crear archivo acme.json y cambiar permisos
```
touch acme.json && chmod 600 acme.json
```

agregar registro dns para creacion y validacion de certificados de Lets encrypt.
```
crear registro para subdominio traefikdocker.domain.com
```

Crear docker network que sera usada por traefik
```
docker network create web
```

sustituir dominio y ejecutar este comando adentro de la carpeta docker
```
docker run -d \
  -v /var/run/docker.sock:/var/run/docker.sock \
  -v $PWD/traefik.toml:/traefik.toml \
  -v $PWD/acme.json:/acme.json \
  -p 80:80 \
  -p 443:443 \
  -l traefik.frontend.rule=Host:traefikdocker.domain.com \
  -l traefik.port=8080 \
  --network web \
  --name traefik \
  traefik:1.7.2-alpine
```


Crear registro DNS , en tu proveedor de dns. usaremos los siguientes subdominios.

```
http1,http2,jenkins,blogprueba
```

abrir subdominio de traefik
```
traefikdocker.ejemplo.com
```


verificar status desde consola

```
 curl -s -u admin:"ULTRAPASSWORD" https://traefikdocker.domain.com/health | jq .

```

test stats , esto retornara un 401 cada vez que se ejecute
```
while true ; do curl -s  https://traefikdocker.domain.com/health ; done
```

sustituir domain.com por tu dominio en cada uno de los docker-compose.yml


probar ruteo escalando servicio
```
docker-compose scale machine=3
```
