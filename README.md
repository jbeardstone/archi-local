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
