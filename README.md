# Docker-TP_Eval
Groupe Yoni MICHARD, Semy BOUACID

Prérequis :

Installer l’image de Prestashop : 
docker pull prestashop/prestashop:latest

TASK 1 :
1.	Créer un réseau Docker - Réseau PrestaShop :
•	docker network create prestashop-network

 2.	Exécuter les conteneurs Frontend et Database dans le réseau :
  
docker run --name containeur-bd -d -e POSTGRES_USER=admin -e POSTGRES_PASSWORD=admin -e POSTGRES_DB=evaldb -v tp_volume:/var/lib/postgresql/data -p 5433:5432 postgres
docker network connect prestashop-network conteneur-bd
docker run --name conteneur-prestashop --network prestashop-network -e conteneur-db_PASSWORD=admin -d prestashop/prestashop
docker exec -it conteneur-prestashop bash
apt-get update
apt-get install postgresql-client
psql -h conteneur-db -U admin -d evaldb -W

TASK 2

1.	Créer des réseaux avec des CIDR - Frontend et Backend :
•	docker network create --subnet=192.168.1.0/24 eval-frontend-network 

•	docker network create --subnet=192.168.2.0/24 eval-backend-network 

2.	Créer un conteneur passerelle/router :

•	docker run -d --name gateway --network eval-frontend-network nginx
•	docker network connect eval-backend-network gateway

3.	Configurer la table de routage à l'intérieur du conteneur passerelle/router :

On doit d’abord récupérer la passerelle des deux sous-réseaux crées :
•	docker network inspect eval-frontend-network
•	docker network inspect eval-backend-network

On ajout les deux addresses de passerelle aux commandes qui nous permettent de configure les tables de routage :
•	docker exec -it gateway /bin/bash ip route 192.168.1.0 via 192.168.1.2 ip route add 192.168.2.0 via 192.168.2.2
•	docker exec --privileged gateway ip route replace 192.168.1.0 via 192.168.1.2
•	docker exec --privileged gateway ip route replace 192.168.2.0 via 192.168.2.2

Vérifier la connectivité en utilisant les adresses IP :
À l'intérieur du conteneur passerelle/router :

ping <container_IP_eval-frontend-network> ping <container_IP_eval-backend-network> 
Soumission



