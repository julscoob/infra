# Configuration de la répartition de charge


### 1. Création du groupe de haute disponibilité
*cf screenshot*


On créé le groupe de haute disponibilité sur la base du groupe de ressources déjà créé.

On laisse les valeurs par défaut pour les domaines d'érreur et de mise à jour.


### 2. Création des serveurs de prod
*cf screenshot*

Adresse ip locales :
- srv-prod1 : 10.0.1.7
- srv-prod2 : 10.0.1.8
- srv-prod3 : 10.0.1.9

Lors de la configuration des fonctionnalités on spécifie le groupe de haute disponibilité prod-dispo.

### 3. Création du loadbalancer

On créé le loadbalancer avec les attributs par défaut et on lui attribut on addresse ip publique.

Ensuite, on ajoute un pool principal au loadbalancer que l'on associe au groupe de haute disponibilité contenant nos 3 serveurs de prod.
*cf screenshot*

### 4. Ajout des sondes d'intégrité

Pour permettre le loadbalancement sur les différents ports de nos machines on autorise d'abord le traffic sur ces derniers dans le groupe de sécurité.

On créé ensuite deux sondes d'intégrité permettant du surveiller le traffic entrant sur les ports 80 (HTTP) et 443 (HTTPS).
*cf screenshot*

Enfin, une fois les sondes créées nous mettons en place deux règles d'équilibrage de charge liées au sondes et au pool principal du loadbalancer.

Dorénavant, les requêtes reçues sur le loadbalancer par le port 80 ou 443 seront redirigées et réparties sur les différents serveurs de prod.
