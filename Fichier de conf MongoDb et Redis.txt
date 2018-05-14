Fichier de conf:


   * MongoDB :

- Configurer le repertoire de données

mkdir /data/mongo-metadata
chown -R mongod:mongod /data/mongo-metadata/


- Configuration du noeud

Configuration du nœud
dbPath = /data/mongo-metadata/
bindIp = 0.0.0.0
replication:
  replSetName: replica-name or config-name
sharding:
  clusterRole: shardsvr or configsvr




   *Redis : 

- Installez les dépendances build and test
$ sudo apt-get update
$ sudo apt-get install build-essential tcl


- Telecharger et extraire le code source 
$ cd /tmp
$ curl -O http://download.redis.io/redis-stable.tar.gz
$ tar xzvf redis-stable.tar.gz


-Installer Redis
$ cd redis-stable
$ make	// on compile les binaires Redis
$ make test	// on effectue un test pour verifier que tout fonctionne 
$ sudo make install	// on Installe les binaires redis


- Configurer redis
$ sudo mkdir /etc/redis    // on cree un repertoire de configuration 
$ sudo cp /tmp/redis-stable/redis.conf /etc/redis
$ sudo nano /etc/redis/redis.conf
$ vim redis.conf   // on modifie le fichier redis.conf (supervised no en supervised systemd)
// spécifier le répèrtoire que redis utiliseras pour vides les données (dir var/lib/redis) et enregistrer et fermer

// creer et ouvrez le fichier redis.ervice
$ sudo vim /etc/systemd/redis.service

>> Rajouter une secrtion Unit en définnissant une exigence selon laquelle la mise en réseau doit être dispo avant de commencer
[Unit]
Description=Redis In-Memory Data Store
After=network.target

>> Rajouter une section Sercice

[Service]
User=redis
Group=redis
ExecStart=/usr/local/bin/redis-server /etc/redis/redis.conf
ExecStop=/usr/local/bin/redis-cli shutdown
Restart=always

>> section install 
[Install]
WantedBy=multi-user.target




-  Créer l'utilisateur, le droupe et les répertoires Redis


>> creer utilisateurs et goupe

$ sudo adduser --system --group --no-create-home redis

>> creer le repertoire redis

$ sudo mkdir /var/lib/redis

>> definir propriétéde repertoire

$ sudo chown redis:redis /var/lib/redis
$ sudo chmod 770 /var/lib/redis

- Demarrez le service Redis
$ sudo systemctl start redis

>> j'ai effectué des test pour tester le bon fonctionement 
$ redis-cli 

etc..

$ sudo systemctl restart redis
$ redis-cli

etc..

$ exit

>> Activer redis pour demarrer au demarrage
$ sudo systemctl enable redis