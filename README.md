# README -
Objectif : --> fichier PDF --> [Uploading Readme KWANTWOO Williams rattrapage + exos.pdf…]()


Déployer l'outil de monitoring cAdvisor dans Docker Swarm pour surveiller l'utilisation des ressources des conteneurs en temps réel.
 Prérequis
Avant de commencer, assurez-vous que :
- Docker est installé
- Docker Swarm est initialisé sur votre machine
Étape 1 – Créer le fichier docker-compose-cadvisor.yml
Créez un fichier `docker-compose-cadvisor.yml` avec le contenu suivant :
version: '3.5'

services:
  cadvisor:
    image: gcr.io/cadvisor/cadvisor:latest
    deploy:
      mode: replicated
      replicas: 1
      placement:
        constraints:
          - node.role == manager
    ports:
      - "8081:8080"
    volumes:
      - /:/rootfs:ro
      - /var/run:/var/run:ro
      - /sys:/sys:ro
      - /var/lib/docker/:/var/lib/docker:ro
    networks:
      - monitor-net

networks:
  monitor-net:

Étape 2 – Déployer la stack cAdvisor
Utilisez la commande suivante pour déployer la stack :
docker stack deploy -c docker-compose-cadvisor.yml monitor
 Étape 3 – Vérifier l’état de la stack
Liste des stacks :
docker stack ls
Liste des services de la stack cAdvisor :
docker stack services monitor
Étape 4 – Accéder à l’interface cAdvisor
Ouvrez un navigateur à l’adresse :
http://localhost:8081
Vous verrez l'interface de surveillance des conteneurs.
 
 










README - Déploiement de Zabbix avec Docker Swarm
 Objectif
Déployer une instance complète de Zabbix (serveur, base de données, interface web) en utilisant Docker Swarm sur une machine unique.
 Prérequis
Avant de commencer, assurez-vous que :
- Docker est installé
- Docker Swarm est initialisé sur votre machine
Étape 1 – Créer le fichier docker-compose-zabbix.yml
Créez un fichier `docker-compose-zabbix.yml` avec le contenu suivant :
version: '3.5'

services:
  mariadb:
    image: mariadb:10.5
    environment:
      MYSQL_DATABASE: zabbix
      MYSQL_USER: zabbix
      MYSQL_PASSWORD: zabbix_pass
      MYSQL_ROOT_PASSWORD: root_pass
    volumes:
      - zbx_db:/var/lib/mysql

  zabbix-server:
    image: zabbix/zabbix-server-mysql:alpine-6.0-latest
    depends_on:
      - mariadb
    environment:
      DB_SERVER_HOST: mariadb
      MYSQL_DATABASE: zabbix
      MYSQL_USER: zabbix
      MYSQL_PASSWORD: zabbix_pass
    ports:
      - "10051:10051"

  zabbix-web:
    image: zabbix/zabbix-web-nginx-mysql:alpine-6.0-latest
    depends_on:
      - zabbix-server
    environment:
      DB_SERVER_HOST: mariadb
      MYSQL_DATABASE: zabbix
      MYSQL_USER: zabbix
      MYSQL_PASSWORD: zabbix_pass
      ZBX_SERVER_HOST: zabbix-server
      PHP_TZ: Europe/Paris
    ports:
      - "8090:8080"
      - "8443:8443"

volumes:
  zbx_db:

 Étape 2 – Déployer la stack Zabbix
Utilisez la commande suivante pour déployer la stack :
docker stack deploy -c docker-compose-zabbix.yml zabbix
 Étape 3 – Vérifier l’état de la stack
Liste des stacks :
docker stack ls
Liste des services de la stack Zabbix :
docker stack services zabbix
Étape 4 – Accéder à l’interface Zabbix
Ouvrez un navigateur à l’adresse :
http://localhost:8090
 


Déploiement de Uptime Kuma avec Docker Swarm (Mono-node)
Objectif
Déployer l'application Uptime Kuma à l'aide de Docker Swarm sur une machine unique (mono-node), en utilisant un fichier docker-compose.yml et la commande docker stack deploy.
 Prérequis
Docker doit être installé sur la machine.
Pour vérifier l’installation de Docker :
docker --version
Étape 1 – Initialiser le cluster Swarm
Initialiser Docker Swarm en tant que node manager :
docker swarm init
Étape 2 – Créer le fichier docker-compose.yml
Créer un dossier dédié :
mkdir uptime-kuma-swarm
cd uptime-kuma-swarm
Créer le fichier docker-compose.yml dans ce dossier :
version: '3.3'
services:
  uptime-kuma:
    image: louislam/uptime-kuma:latest
    ports:
      - "3001:3001"
    volumes:
      - ./data:/app/data
    restart: always
Étape 3 – Déployer la stack Uptime Kuma
Lancer la stack depuis le dossier contenant le fichier :
docker stack deploy -c docker-compose.yml uptime-kuma
Étape 4 – Vérifier le déploiement
Afficher la liste des stacks :
docker stack ls
Lister les services de la stack Uptime Kuma :
docker stack services uptime-kuma
Lister les conteneurs déployés :
docker stack ps uptime-kuma
Accéder à l'interface Uptime Kuma depuis un navigateur:
http://localhost:3001



README - Déploiement de Portainer avec Docker Swarm
Objectif
Déployer l'interface de gestion Portainer pour administrer notre cluster Docker Swarm via une interface web.
Prérequis
- Docker est installé
- Docker Swarm est initialisé sur votre machine
Étape 1 – Créer le fichier docker-compose-portainer.yml
Créez un fichier `docker-compose-portainer.yml` avec le contenu suivant :
version: '3.5'

networks:
  portainer-net:

services:
  portainer:
    image: portainer/portainer-ce:latest
    command: -H unix:///var/run/docker.sock
    ports:
      - "9000:9000"
    volumes:
      - /var/run/docker.sock:/var/run/docker.sock
      - portainer_data:/data
    networks:
      - portainer-net
    deploy:
      mode: replicated
      replicas: 1
      placement:
        constraints: [node.role == manager]

volumes:
  portainer_data:

Étape 2 – Déployer la stack Portainer
Utilisez la commande suivante pour déployer la stack :
docker stack deploy -c docker-compose-portainer.yml portainer
Étape 3 – Vérifier l’état de la stack
Liste des stacks :
docker stack ls
 

Liste des services de la stack Portainer :
docker stack services portainer
 




Étape 4 – Accéder à l’interface Portainer
Ouvrez un navigateur à l’adresse :
http://localhost:9000 
  













Objectif
créer quelques conteneurs ou pods simples (Genre Wordpress, Adminer, Nginx ou autre) depuis l'interface de Portainer et assurez vous que cela fonctionne dans votre cluster.

Etapes : 
Il faudra se render dans “Environnements” puis sélectionner “Socket”  choisir le nom que l’on souhaite ici “SWARM” et cliquer sur “Connect”
 


Puis comme le démontre l’image ci-dessous, il faudra sélectionner “Swarm” puis aller dans “Stacks”.
 
Nous avons la liste de nos stacks, mais nous allons en créer de nouveaux. Pour cela il faudra aller dans « Add stack »

 


Nous allons ajouter Wordpress : comme le démontre l’image ci-dessous. Et ajouter le YAML. Puis nous pouvons valider en cliquant sur « Deploy the stack ».
 
version: '3.8'

services:
  db:
    image: mariadb:latest
    environment:
      MYSQL_ROOT_PASSWORD: example
      MYSQL_DATABASE: wordpress
      MYSQL_USER: wordpress
      MYSQL_PASSWORD: wordpress
    volumes:
      - db_data:/var/lib/mysql

  wordpress:
    image: wordpress:latest
    environment:
      WORDPRESS_DB_HOST: db:3306
      WORDPRESS_DB_USER: wordpress
      WORDPRESS_DB_PASSWORD: wordpress
      WORDPRESS_DB_NAME: wordpress
    ports:
      - "8082:80"
    depends_on:
      - db

volumes:
  db_data:


 
 


Le principe est le même pour Adminer : Voici les étapes de création qui inclus aussi le YAML.
 
version: '3.8'

services:
  adminer:
    image: adminer:latest
    ports:
      - "8083:8080"
----
 



Le principe est le même pour Nginx : Voici les étapes de création qui inclus aussi le YAML.

 
version: '3.8'

services:
  nginx:
    image: nginx:latest
    ports:
      - "8084:80"

 

A présent lorsque l’on fait notre petit check et notre petite vérification on peut voir les résultats suivants :
 
 
 

Pour Résumé: 
Utilisation de Portainer
•	Déploiement exclusivement depuis l’UI Portainer → Stacks → Add Stack.
________________________________________
 Vérification du bon fonctionnement
•	WordPress : Installateur visible → http://localhost:8082
•	Adminer : Connecté à la DB via db → http://localhost:8083
•	NGINX : Page par défaut visible → http://localhost:8084


Swarm actif :
•	Tout tourne comme services Swarm, pas en conteneur standalone.
•	Vérification via Portainer UI : Stacks, Services, Logs → tout est Running.



Objectif: 
Mesurer la charge de notre cluster ou instance Docker avec Cadvisor dans Prometheus ou Grafana, ou Zabix.

Ajoute un Web Scenario
•	Clique sur l’onglet Web Scenarios du Host.
•	Create Web Scenario
o	Name : cAdvisor - Container Metrics
o	URL : http://host.docker.internal:8081/api/v1.3/subcontainers
(ou http://127.0.0.1:8081/api/v1.3/subcontainers si Zabbix Server est sur la même machine)
o	Steps : 1
o	Interval : 60s
________________________________________
Ajoute un Item pour exploiter la réponse
•	Dans le même Host → Items → Create Item
o	Name : cAdvisor JSON
o	Type : Dependent Item ou HTTP Agent
o	Key : cadvisor.raw
o	URL : http://host.docker.internal:8081/api/v1.3/subcontainers
o	Type of information : Text
 3) Déploiement
3.1) Gitea
Gitea est une forge Git auto-hébergée.
docker-compose-gitea.yml :

version: "3"

services:
  gitea:
    image: gitea/gitea:latest
    container_name: gitea
    restart: always
    ports:
      - "3000:3000"
      - "222:22"
    volumes:
      - ./gitea:/data

Commande : docker compose -f docker-compose-gitea.yml up -d
Accès : http://localhost:3000
3.2) Netdata
Netdata permet de superviser la charge et les conteneurs en temps réel.
docker-compose-netdata.yml :

version: "3"

services:
  netdata:
    image: netdata/netdata:latest
    container_name: netdata
    ports:
      - "19999:19999"
    cap_add:
      - SYS_PTRACE
    security_opt:
      - apparmor:unconfined
    volumes:
      - netdataconfig:/etc/netdata
      - netdatalib:/var/lib/netdata
      - netdatacache:/var/cache/netdata
      - /etc/passwd:/host/etc/passwd:ro
      - /etc/group:/host/etc/group:ro
      - /proc:/host/proc:ro
      - /sys:/host/sys:ro
      - /etc/os-release:/host/etc/os-release:ro

volumes:
  netdataconfig:
  netdatalib:
  netdatacache:

Commande : docker compose -f docker-compose-netdata.yml up -d
Accès : http://localhost:19999
3.3) whoami
whoami est un conteneur minimaliste pour tester vos routes réseau.
docker-compose-whoami.yml :

version: "3"

services:
  whoami:
    image: containous/whoami
    container_name: whoami
    ports:
      - "8082:80"

Commande : docker compose -f docker-compose-whoami.yml up -d
Accès : http://localhost:8082
4)  Fichiers utiles

- docker-compose-gitea.yml
- docker-compose-netdata.yml
- docker-compose-whoami.yml

      
