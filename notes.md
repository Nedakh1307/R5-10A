## TP1
## Étape 1 — Lecture

**Q1.** Le fichier sert pour tout le module,à chaque séance on ajoute un moteur de base de données à l'application comme ça tout est déjà prêt et on ne reconfigure rien. Pour aujourd'hui on n'utilise que PostgreSQL.

**Q2.** C'est Redis qui n'a pas de volume ,ses données sont donc stockées uniquement dans le conteneur ,elles restent si on fait `stop` mais elles sont perdues si on supprime le conteneur avec `down`.

**Q3.** Le format est `port_machine:port_conteneur`, 5432 est le port de PostgreSQL à l'intérieur du conteneur et 15432 est le port qu'on utilise depuis notre machine. Docker redirige l'un vers l'autre et le port est décalé pour ne pas gêner un PostgreSQL déjà installé sur la machine.

**Q4.** Sur le TP oui,c'est un environnement local avec des données de test, personne d'autre n'y accède. 
Sur un serveur de production non ,n'importe qui qui a accès au fichier voit les mots de passe il faudrait les mettre dans des variables d'environnement ou un gestionnaire de secrets.

## Étape 2 — Démarrage

**Q5.** `docker compose exec` lance une commande dans un conteneur qui tourne déjà, la commande `redis-cli` s'exécute donc à l'intérieur du conteneur Redis, pas sur ma machine (je n'ai pas besoin d'installer redis-cli).

## Étape 3 — Application

**Q6.** Docker ouvre le port 15432 sur ma machine et le redirige vers le port 5432 du conteneur. Donc pour mon application la base est accessible sur localhost:15432.

**Q7.** Non, les joueurs ne sont pas dupliqués, dans Program.cs la condition `if (!db.Players.Any())` n'ajoute les joueurs que si la table est vide, au deuxième lancement elle contient déjà les 3 joueurs donc rien n'est ajouté.

## Étape 4 — Arrêt


## TP2
## Étape 1 

**Q1.** Le `_id` est fabriqué par l'outil qui envoie les documents (mongoimport ou le driver) pas par le serveur MongoDB. Au TP1 l'Id 4 de Pixel était choisi par PostgreSQL au moment de l'insertion donc on ne le connaissait qu'après.


**Q2.** En SQL normalisé il faudrait une table de liaison `jeu_plateforme` et un `JOIN` avec `WHERE plateforme = 'Switch'`. 
En MongoDB, la valeur est directement dans le tableau donc la requête est plus simple.

**Q3.** Le compte `pixelhub` est créé dans la base `admin`pas dans la base `pixelhub`, sans `--authenticationDatabase admin`MongoDB cherche le compte au mauvais endroit et refuse la connexion.

**Q4.** C'est pratique au début mais c'est un problème à 4 développeurs sur 6 mois : 
chacun invente ses champs avec des noms ou des formats différents et les documents deviennent incohérents. Personne ne contrôle rien et on ne sait plus quels champs existent.

**Q5.** on s'attend à une erreur `Jeux` n'est pas `jeux` parce que MongoDB est sensible à la casse, il cherche dans une collection `Jeux` qui n'existe pas et renvoie un résultat vide sans erreur car une collection inexistante n'est pas considérée comme une faute.

**Q6.**MongoInvalidArgumentError: Update document requires atomic operators
MongoDB refuse parce que `updateOne` attend des opérateurs comme `$set` ce qui évite d'écraser tout le document par erreur, pour remplacer vraiment le document en entier on utilise `replaceOne`.

**Q7.** En SQL il faudrait `ALTER TABLE jeux ADD COLUMN nbVotes int;`, toutes les autres lignes auraient aussi cette colonne avec la valeur `NULL`.