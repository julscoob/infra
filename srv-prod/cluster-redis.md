# Installation d'un cluster Redis

Nous avons 3 serveurs qui sont :
```
srv-prod1 :  10.0.1.7
srv-prod2 :  10.0.1.8
srv-prod3 :  10.0.1.9
```

### 1. Installation des paquets
Installation des dépendances
```
sudo apt-get update && sudo apt-get upgrade
sudo apt install make gcc libc6-dev tcl
```
Téléchargement de Redis
```
wget http://download.redis.io/redis-stable.tar.gz
tar xvzf redis-stable.tar.gz
cd redis-stable
sudo make install
```
```
make install
```
On répète ces étapes sur nos 3 serveurs de production.

### 2. Configuration de Redis
Nous aurons 3 machines interconnectés entre elles comme cela : 

![alt text](https://image.ibb.co/caegAo/redis_cluster_3_nodes.png)

Ainsi sur le serveur 1 on configure Redis :
```
cp redis.conf c_slave.conf
mv redis.conf a_master.conf
```
Dans le fichier a_master.conf on vas modifier ces valeurs :
```
# bind 127.0.0.1
protected-mode no
port 6379
pidfile /var/run/redis_6379.pid
cluster-enabled yes
cluster-config-file nodes-6379.conf
cluster-node-timeout 15000
```
Dans le fichier c_slave.conf on va modifier ces valeurs :
```
# bind 127.00.1
protected-mode no
port 6381
pidfile /var/run/redis_6381.pid
cluster-enabled yes
cluster-config-file nodes-6381.conf
cluster-node-timeout 15000
```
On répète ces mêmes étapes sur les 3 serveurs avec les ports suivants :
```
Server	Master	Slave
1	6379	6381
2	6380	6379
3	6381	6380
```
Ensuite on va lancer les instances sur les 3 serveurs :
```
redis-server redis-stable/a_master.conf
redis-server redis-stable/c_slave.conf
```

Lorsque les instances sont lancés elles doivent afficher un message de ce type :

![alt text](https://image.ibb.co/mLHZVo/instance.png)

### 3. Création du cluster avec les master
```
sudo apt install ruby
gem install redis
```

Ensuite on va executer cette commande sur le serveur 1 afin de lancer le cluster :

```
redis-stable/src/redis-trib.rb create 10.0.1.7:6379 10.0.1.8:6380 10.0.1.9:6381
```

Ce qui nous donne : 

![alt text](https://image.ibb.co/dLMHi8/cluster.png)

Pour voir les noeuds actifs du cluster on fait :
```
redis-cli -c -h 10.0.1.7 -p 6379
10.0.1.7 > cluster nodes
```

### 4. Ajouter des slaves

Nous allons maintenant ajouter les slaves toujours selon ce schéma : 

![alt text](https://image.ibb.co/caegAo/redis_cluster_3_nodes.png)

Avec ces commandes :
```
redis-stable/src/redis-trib.rb add-node --slave --master-id 086ad88798f59f3710fde3d6132c6c9caadee911 10.0.1.7:6381 10.0.1.9:6381
redis-stable/src/redis-trib.rb add-node --slave --master-id 906b68c836381466ca275426cf6cbcb306cb56cf 10.0.1.8:6379 10.0.1.7:6379
redis-stable/src/redis-trib.rb add-node --slave --master-id 28cfbfcafe7d1b033fbcaa38468569044a164e92 10.0.1.9:6380 10.0.1.8:6380
```
Elles doivent retourner cela :

![alt text](https://image.ibb.co/mskci8/slave.png)

Maintenant nous avons notre cluster :

![alt text](https://image.ibb.co/g9LA38/clusterfinal.png)





