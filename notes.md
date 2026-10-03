## Étape 1 — Lecture

**Q1.** Le fichier sert pour tout le module,à chaque séance on ajoute un moteur de base de données à l'application comme ça tout est déjà prêt et on ne reconfigure rien. Pour aujourd'hui on n'utilise que PostgreSQL.

**Q2.** C'est Redis qui n'a pas de volume ,ses données sont donc stockées uniquement dans le conteneur ,elles restent si on fait `stop` mais elles sont perdues si on supprime le conteneur avec `down`.

**Q3.** Le format est `port_machine:port_conteneur`, 5432 est le port de PostgreSQL à l'intérieur du conteneur et 15432 est le port qu'on utilise depuis notre machine. Docker redirige l'un vers l'autre et le port est décalé pour ne pas gêner un PostgreSQL déjà installé sur la machine.

**Q4.** Sur le TP oui,c'est un environnement local avec des données de test, personne d'autre n'y accède. 
Sur un serveur de production non ,n'importe qui qui a accès au fichier voit les mots de passe il faudrait les mettre dans des variables d'environnement ou un gestionnaire de secrets.

## Étape 2 — Démarrage

**Q5.** `docker compose exec` lance une commande dans un conteneur qui tourne déjà, la commande `redis-cli` s'exécute donc à l'intérieur du conteneur Redis, pas sur ma machine (je n'ai pas besoin d'installer redis-cli).