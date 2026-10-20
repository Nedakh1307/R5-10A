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


## Étape 2 
**Q8.** Dans `IGameCatalog` le mot « Mongo » n’apparaît nulle part. 
C’est important car l’interface définit uniquement les opérations du catalogue sans dépendre de la technologie utilisée pour stocker les données, on peut donc utiliser MongoDB ou une autre base de données sans modifier l’interface.


**Q9.**Le driver MongoDB obtient une erreur de sérialisation sur le champ `Id`,le driver essaie de convertir le champ `_id` du document MongoDB en `ObjectId` mais certains documents ne correspondent pas au type attendu par le modèle C#, il ne sait donc pas désérialiser correctement la valeur dans la propriété `Id` de `Game`.

**Q10.** `[BsonIgnoreExtraElements]` permet au driver d’ignorer les champs présents dans MongoDB mais qui ne sont pas définis dans le modèle `Game`,en contrepartie ces données sont perdues lors de la désérialisation, ainsi `joueursParEquipe` n’apparaît pas dans la réponse de `/games` car ce champ existe dans MongoDB mais pas dans la classe `Game`.


**Q11.**`[BsonIgnoreExtraElements]` est simple et permet d’ignorer les champs MongoDB qui ne sont pas présents dans la classe C# mais ces champs sont perdus. 
`[BsonExtraElements]` permet de récupérer et conserver les champs supplémentaires ce qui est plus flexible mais moins typé. 
`FpsGame : Game` permet de représenter précisément les données propres à chaque type de jeu et offre un meilleur typage, mais elle est plus complexe à maintenir et moins flexible si le schéma change souvent.

**Vérification obligatoire**

tattou@debian1:~/Bureau/Neda/BUT3_S5/r5.10/r5.10$ curl http://localhost:5199/players
[{"id":1,"pseudo":"Nova","coins":1200},{"id":2,"pseudo":"Krayz","co

tattou@debian1:~/Bureau/Neda/BUT3_S5/r5.10/r5.10$ curl http://localhost:5199/games5/r5.10/r5.10$ curl http://localhost:5199/games
[{"id":{"timestamp":1791201897,"creationTime":"2026-10-05T12:04:57Z"},"titre":"Terraria","genre":"Bac à sable","note":4.7,"anneeSortie":2011,"plateformes":["PC","Switch","PS5","Mobile"],"tags":["exploration","construction","coopératif","sandbox"],"specifiques":{"joueursMax":8,"mondesInfinis":true}},{"id":{"timestamp":1791201897,"creationTime":"2026-10-05T12:04:57Z"},"titre":"Baldur's Gate 3","genre":"RPG","note":4.9,"anneeSortie":2023,"plateformes":["PC","PS5","Xbox Series"],"tags":["narratif","tour par tour","coopératif"],"specifiques":{"nombreClasses":12,"dureeCampagneHeures":75,"choixNarratifs":true}},{"id":{"timestamp":1791201897,"creationTime":"2026-10-05T12:04:57Z"},"titre":"Mario Kart 8 Deluxe","genre":"Course","note":4.6,"anneeSortie":2017,"plateformes":["Switch"],"tags":["course","famille","multijoueur local"],"specifiques":{"nombreCircuits":96,"nombrePersonnages":42,"modeMiroir":true}},{"id":{"timestamp":1791201897,"creationTime":"2026-10-05T12:04:57Z"},"titre":"Cities: Skylines II","genre":"Gestion","note":3.8,"anneeSortie":2023,"plateformes":["PC","PS5","Xbox Series"],"tags":["construction","simulation","solo"],"specifiques":{"scenarios":["Île","Vallée","Désert"],"modeBacASable":true,"dureeMoyennePartieHeures":14}},{"id":{"timestamp":1791201897,"creationTime":"2026-10-05T12:04:57Z"},"titre":"Vampire Survivors","genre":"Rogue-like","note":4.4,"anneeSortie":2022,"plateformes":["PC","Switch","Mobile"],"tags":["rogue-like","arcade","solo"],"specifiques":{"nombrePersonnages":40,"generationProcedurale":true}},{"id":{"timestamp":1791201897,"creationTime":"2026-10-05T12:04:57Z"},"titre":"Valorant","genre":"FPS","note":4.3,"anneeSortie":2020,"plateformes":["PC"],"tags":["compétitif","tir","héros"],"specifiques":{"joueursParEquipe":5,"nombreAgents":26,"classementCompetitif":true,"competitif":true}},{"id":{"timestamp":1791201897,"creationTime":"2026-10-05T12:04:57Z"},"titre":"Stardew Valley","genre":"Simulation","note":4.8,"anneeSortie":2016,"plateformes":["PC","Switch","PS5","Mobile"],"tags":["pixel art","détente","coopératif"],"specifiques":{"nombreSaisons":4,"coop":true,"joueursMax":4}},{"id":{"timestamp":1791201897,"creationTime":"2026-10-05T12:04:57Z"},"titre":"Counter-Strike 2","genre":"FPS","note":4.5,"anneeSortie":2023,"plateformes":["PC"],"tags":["compétitif","tir","équipe"],"specifiques":{"joueursParEquipe":5,"cartes":["Dust II","Mirage","Inferno","Nuke"],"classementCompetitif":true,"competitif":true}},{"id":{"timestamp":1791201897,"creationTime":"2026-10-05T12:04:57Z"},"titre":"Hades II","genre":"Rogue-like","note":4.7,"anneeSortie":2024,"plateformes":["PC","Switch"],"tags":["rogue-like","action","solo"],"specifiques":{"nombreArmes":6,"generationProcedurale":true}},{"id":{"timestamp":1791201897,"creationTime":"2026-10-05T12:04:57Z"},"titre":"Rocket League","genre":"Sport","note":4.3,"anneeSortie":2015,"plateformes":["PC","PS5","Xbox Series","Switch"],"tags":["compétitif","voiture","arcade"],"specifiques":{"joueursParEquipe":3,"classementCompetitif":true}},{"id":{"timestamp":1791203083,"creationTime":"2026-10-05T12:24:43Z"},"titre":"Un jeu de test","genre":"Puzzle","note":3.5,"anneeSortie":0,"plateformes":[],"tags":[],"specifiques":{"nombreNiveaux":120,"champCompletementInvente":"ça marche quand même"}}]

tattou@detattou@debian1:~/Bureau/Neda/BUT3_S5/r5.10/r5.10$ curl http://localhost:5199/games/genre/FPS
[{"id":{"timestamp":1791201897,"creationTime":"2026-10-05T12:04:57Z"},"titre":"Valorant","genre":"FPS","note":4.3,"anneeSortie":2020,"plateformes":["PC"],"tags":["compétitif","tir","héros"],"specifiques":{"joueursParEquipe":5,"nombreAgents":26,"classementCompetitif":true,"competitif":true}},{"id":{"timestamp":1791201897,"creationTime":"2026-10-05T12:04:57Z"},"titre":"Counter-Strike 2","genre":"FPS","note":4.5,"anneeSortie":2023,"plateformes":["PC"],"tags":["compétitif","tir","équipe"],"specifiques":{"joueursParEquipe":5,"cartes":["Dust II","Mirage","Inferno","Nuke"],"classementCompetitif":true,"c

tattou@debian1:~/Bureau/Neda/BUT3_S5/r5.10/r5.10$ curl http://localhost:5199/games/top/3t:5199/games/top/3
[{"id":{"timestamp":1791201897,"creationTime":"2026-10-05T12:04:57Z"},"titre":"Baldur's Gate 3","genre":"RPG","note":4.9,"anneeSortie":2023,"plateformes":["PC","PS5","Xbox Series"],"tags":["narratif","tour par tour","coopératif"],"specifiques":{"nombreClasses":12,"dureeCampagneHeures":75,"choixNarratifs":true}},{"id":{"timestamp":1791201897,"creationTime":"2026-10-05T12:04:57Z"},"titre":"Stardew Valley","genre":"Simulation","note":4.8,"anneeSortie":2016,"plateformes":["PC","Switch","PS5","Mobile"],"tags":["pixel art","détente","coopératif"],"specifiques":{"nombreSaisons":4,"coop":true,"joueursMax":4}},{"id":{"timestamp":1791201897,"creationTime":"2026-10-05T12:04:57Z"},"titre":"Terraria","genre":"Bac à sable","note":4.7,"anneeSortie":2011,"plateformes":["PC","Switch","PS5","Mobile"],"tags":["exploration","construction","coopératif","sandbox"],"specifiques":{"joueursMax":8,"mondesInfinis":true}}]

## Étape 3

**Q12.**
```sql
SELECT genre, AVG(note) AS note_moyenne, COUNT(*) AS nombre
FROM jeux
GROUP BY genre
ORDER BY note_moyenne DESC;
```
Les deux requêtes font la même chose elles regroupent par genre,calculent la moyenne et le nombre de jeux.


# TP3

## Partie 1 : redis-cli

**Q1.** Atomique = l'opération se fait d'un seul bloc, personne ne peut s'intercaler au milieu. Si mille joueurs terminent une partie en même temps, tous les +1 sont comptés, aucun n'est perdu. En SQL, `UPDATE ... SET n = n + 1` est aussi atomique, mais si on fait un SELECT puis un UPDATE, deux requêtes peuvent lire la même valeur et on perd un incrément. La différence : Redis le fait nativement et très vite, SQL passe par des verrous et une transaction.

**Q2.** Il faudrait : lire le JSON (GET), le désérialiser, ajouter 250 à xp, le resérialiser, puis le réécrire (SET). Si deux requêtes le font en même temps, elles lisent la même ancienne valeur et la dernière écrase l'autre : une mise à jour est perdue. `HINCRBY` fait tout ça en une seule opération atomique.

**Q3.** `ZRANGE` trie du plus petit score au plus grand, `ZREVRANGE` du plus grand au plus petit. Pour un top 3, j'utilise `ZREVRANGE`.

**Q4.** `ZREVRANK` renvoie un index qui commence à 0. Le meilleur joueur verrait « 0e ». Il faut ajouter 1 avant l'affichage.

**Q5.** `LPUSH` ajoute à gauche et `RPOP` retire à droite, donc c'est Nova (la première arrivée) qui sort : c'est une file FIFO. `LPOP` aurait sorti Ombre (la dernière arrivée) : ce serait une pile (LIFO), pas juste pour du matchmaking.

**Q6.** Juste après le SET : TTL vaut environ 30 et EXISTS vaut 1. Après 30 secondes : TTL vaut -2 et EXISTS vaut 0. Sur une clé sans expiration (`compteur:parties`), TTL vaut -1. Donc : nombre positif = secondes restantes, -1 = la clé existe sans expiration, -2 = la clé n'existe pas.

**Q7.** Il faudrait une colonne date d'expiration et une tâche planifiée qui fait `DELETE ... WHERE expire_at < now()`. Risques : la tâche plante ou est oubliée, les lignes expirées restent visibles entre deux passages si on ne filtre pas, et la tâche charge la base.

## Partie 2 :
**Q8.** Un `ConnectionMultiplexer` gère la connexion à Redis et la partage entre toutes les requêtes. Il est fait pour être créé une seule fois. Si on en créait un par requête, on ouvrirait trop de connexions (lent, et on finit par saturer Redis). Le bug est invisible en TP car on est seul à tester, avec très peu de requêtes.

**Q9.** Sans `Order.Descending`, `/leaderboard/top/5` affiche les plus mauvais scores en premier (Pixel, 1800, devant Ombre, 10200). Ce n'est pas visiblement faux : la réponse est bien formée, sans erreur, avec de vrais joueurs et de vrais scores. Ce bug est dangereux car le code compile et renvoie un 200, mais le classement est à l'envers, et on ne le remarque qu'en regardant les valeurs.

**Q12.** J'ai modifié 0 ligne dans les endpoints. Ils dépendent de l'interface `IGameCatalog`, pas de `MongoGameCatalog` : j'ai juste changé l'enregistrement dans `Program.cs` pour injecter `CachedGameCatalog`, qui implémente la même interface. C'est le choix de la séance 2 (passer par une interface / injection de dépendances) qui rend cela possible.