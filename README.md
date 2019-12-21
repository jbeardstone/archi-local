# Dev base Docker

## Outils de base

* Proxy server (Traefik)
* Serveur page d'accueil de l'environnement (nginx)
* Administration
  * pg-admin
  * redis-commander
  * autres...

## Outils connexes

* mkcert
* json-server
* telnet, curl, wget (optionnel)

## Volumes

* pour chaque container
  * data
  * config

## Réseaux

* principal (web -> 80)
* db
* log
* apps

## Création des certificats

```bash
rm -f ./proxy/certs/dev.cert ./proxy/certs/dev.key

mkcert -cert-file ./proxy/certs/dev.cert -key-file ./proxy/certs/dev.key $TCHOO_DOMAIN "*.$TCHOO_DOMAIN" "*.admin.$TCHOO_DOMAIN" $TCHOO_PROJECT_DOMAIN "*.$TCHOO_PROJECT_DOMAIN" docker.localhost "*.docker.localhost" localhost 127.0.0.1 ::1
```


## Installation registry locale

```bash
docker run -d -p 5000:5000 --restart=always --name registry registry:2
```

## Publication locale

```bash
# recopie container
docker tag truc:MM.mm localhost:5000/my-truc
docker push localhost:5000/my-truc
# nettoyage
docker image remove truc:MM.mm
docker image remove localhost:5000/my-truc
# récupération image locale
docker pull localhost:5000/my-truc
# arrêt et suppression registry
docker container stop registry && docker container rm -v registry
```

### Installation / configuration certificats registry

```bash
docker run -d \
  --restart=always \
  --name registry \
  -v "$(pwd)"/certs:/certs \
  -e REGISTRY_HTTP_ADDR=0.0.0.0:443 \
  -e REGISTRY_HTTP_TLS_CERTIFICATE=/certs/domain.crt \
  -e REGISTRY_HTTP_TLS_KEY=/certs/domain.key \
  -p 443:443 \
  registry:2
```

Pour installer registry et le toutim [lire ça](https://github.com/docker/docker.github.io/blob/master/registry/deploying.md)

```bash
registry:
  restart: always
  image: registry:2
  ports:
    - 5000:5000
  environment:
    REGISTRY_HTTP_TLS_CERTIFICATE: /certs/domain.crt
    REGISTRY_HTTP_TLS_KEY: /certs/domain.key
    REGISTRY_AUTH: htpasswd
    REGISTRY_AUTH_HTPASSWD_PATH: /auth/htpasswd
    REGISTRY_AUTH_HTPASSWD_REALM: Registry Realm
  volumes:
    - /path/data:/var/lib/registry
    - /path/certs:/certs
    - /path/auth:/auth
  ```
