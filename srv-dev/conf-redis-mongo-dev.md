# Fichier de configuration du serveur dev


## lamp

On installe un serveur lamp simple.
* linux - apache2 - mysql - php7

* Ensuite, on installe git-auto-pull pour récupérer automatiquement les données des développeurs.
```
rpm install git-auto-pull //nécessite nodejs
node git-auto-pull.js /le/chemin/du/serveur/apache2
```

## MongoDB

### Configurer le repertoire de données

```
mkdir /data/mongo-metadata
chown -R mongod:mongod /data/mongo-metadata/
```

### Configuration du noeud

```
dbPath = /data/mongo-metadata/
bindIp = 0.0.0.0
replication:
  replSetName: replica-name or config-name
sharding:
  clusterRole: shardsvr or configsvr
```



## Redis

Installer les dépendances build and test
```
$ sudo apt-get update
$ sudo apt-get install build-essential tcl
```

Télécharger et extraire le code source 
```
$ cd /tmp
$ curl -O http://download.redis.io/redis-stable.tar.gz
$ tar xzvf redis-stable.tar.gz
```

Installer Redis
```
$ cd redis-stable
$ make	// on compile les binaires Redis
$ make test	// on effectue un test pour verifier que tout fonctionne 
$ sudo make install	// on Installe les binaires redis
```

Configurer redis
```
$ sudo mkdir /etc/redis    // on crée un repertoire de configuration 
$ sudo cp /tmp/redis-stable/redis.conf /etc/redis
$ sudo nano /etc/redis/redis.conf
$ vim redis.conf   // on modifie le fichier redis.conf (supervised no en supervised systemd)
// spécifier le répertoire que Redis utilisera pour vider les données (dir var/lib/redis) et enregistrer et fermer.
```

Créer et ouvrez le fichier redis.service
```
$ sudo vim /etc/systemd/redis.service

//Rajouter une section Unit en définissant une exigence selon laquelle la mise en réseau doit être dispo avant de commencer

[Unit]
Description=Redis In-Memory Data Store
After=network.target

// Rajouter une section Sercice

[Service]
User=redis
Group=redis
ExecStart=/usr/local/bin/redis-server /etc/redis/redis.conf
ExecStop=/usr/local/bin/redis-cli shutdown
Restart=always

>> section install 
[Install]
WantedBy=multi-user.target
```


Créez l'utilisateur, le groupe et les répertoires Redis


Créez les utilisateurs et le goupe
```
$ sudo adduser --system --group --no-create-home redis
```

Créez le repertoire Redis
```
$ sudo mkdir /var/lib/redis
```

Modifiez les règles d'accès au repertoire
```
$ sudo chown redis:redis /var/lib/redis
$ sudo chmod 770 /var/lib/redis
```

Démarrez le service Redis
```
$ sudo systemctl start redis
```

### Test de bon fonctionnement
```
$ redis-cli 

etc..

$ sudo systemctl restart redis
$ redis-cli

etc..

$ exit

>> Activer redis pour demarrer au demarrage
$ sudo systemctl enable redis
```