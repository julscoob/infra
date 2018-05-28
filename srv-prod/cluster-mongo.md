# Cluster MongoDB
Nous avons 3 serveurs qui sont :

```
srv-prod1 :  10.0.1.7
srv-prod2 :  10.0.1.8
srv-prod3 :  10.0.1.9
```
## 0. Configuration préalable :
Assurez-vous préalablement que les différents noeuds sont en capacité de communiquer entre eux. 
De plus il faut egalement remplir le fichier */etc/hosts* Dans notre cas de cette manière. 

    10.0.1.7 srv-prod1
    10.0.1.8 srv-prod2
    10.0.1.9 srv-prod3
    
    

## 1. Installation de la dernière version de MongoDB (3.6.5) :

    sudo apt-key adv --keyserver hkp://keyserver.ubuntu.com:80 --recv 2930ADAE8CAF5059EE73BB4B58712A2291FA4AD5
    echo "deb [ arch=amd64,arm64 ] https://repo.mongodb.org/apt/ubuntu xenial/mongodb-org/3.6 multiverse" | sudo tee /etc/apt/sources.list.d/mongodb-org-3.6.list
    sudo apt update
    sudo apt install -y mongodb-org 

On peut ensuite démarrer le service mongod : 

    sudo systemctl start mongod  
    sudo systemctl enable mongod
## 2. Configuration de mongod : 

    net:  
     port: 27017 
     bindIp: 0.0.0.0
    replication:  
     replSetName: "replica0"
On va ensuite devoir redemarrer le service pour que les modifications entrent en compte : 

    sudo systemctl restart mongod


**Une fois ces actions effectuées sur chacun des noeuds nous allons passer à la configuration de notre cluster**
## 3. Mise en place du Cluster
On va lancer un shell mongo sur le noeud maître du cluster. Dans ce shell nous allons initier le replica de notre noeud, et ajouter à ce dernier les deux serveurs slaves. 

    mongo
    rs.initiate()
    rs.add('srv-prod2:27017')
    rs.add('srv-prod3:27017')
On veut maintenant s'assurer que le tout est bien en place : 
*Il faut avoir préalablement lancé une fois un shell mongo sur chacun des noeuds du cluster.*

    rs.status()
![enter image description here](https://scontent-cdg2-1.xx.fbcdn.net/v/t1.15752-9/33809781_1689502451168118_8821345067676467200_n.png?_nc_cat=0&oh=97479b86b1395ade6c607f6eaffa7114&oe=5BBDFE35)
 
On remarque bien la présence de notre maître comme primary et nos deux autres serveurs comme replica de ce dernier. 
Nous avons donc bien un serveur mongo repliquant ses données sur deux slaves. 
    





