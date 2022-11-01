# Les bonnes pratiques pour optimiser les requêtes:
## Optimiser les requêtes SQL
- Filtrer les données directement via le filtre WHERE et/ou LIMIT, en évitant que cela ne soit fait par l’application.
- Eviter d’utiliser des fonctions dans les clauses de recherche, telle que WHERE.
- Eviter les lectures via “SELECT *” en privilégiant plutôt de lister uniquement les colonnes qui seront exploitées.
- Eviter d’utiliser le wildcard “%” au début d’une recherche LIKE.
    Exemple : une requête “SELECT * FROM table WHERE title LIKE ‘%abc’ est consommateur en performance
- Utiliser des requêtes préparées ou procédures stockées (stored procedure) pour mettre en cache certaines requêtes ou créer un script directement du côté du système de gestion de base de données.

## Optimiser l’usage des requêtes SQL dans l’application
- Compter le nombre de requêtes SQL utilisé au sein d’une page web. Cela permet d’analyser les pages qui pourraient contenir trop d’appels de requêtes SQL, notamment :
- [ ]  Une requête SQL similaire qui est appelée à plusieurs reprises
     Exemple : la requête “SELECT email FROM utilisateur WHERE id = 456”, puis la requête “SELECT date_inscription FROM utilisateur WHERE id = 456” pourrait être extrait en une seule fois.
- [ ] Eviter d’appeler des requêtes SQL dans une boucle.
        Exemple : un requête “SELECT * FROM commentaire WHERE article_id = 123” appelée plusieurs dizaines de fois dans une boucle serait plutôt à remplacer par une requête “SELECT * FROM commentaire WHERE article_id IN (123, 124, 125 …)”.
- Pour un système de pagination, privilégier l’usage de SQL_CALC_FOUND_ROWS, et éviter de faire 2 requêtes, celle contenant les résultats et l’autre pour compter le nombre de résultats total.
- Eviter d’utiliser la requête “WHERE IN” si vous pouvez utiliser “WHERE EXISTS”.
- Eviter les sous-requêtes si une jointure est possible.
- Eviter de compter une colonne (cf. “SELECT COUNT(colonne) FROM table) lorsqu’il faut compter le nombre d’enregistrement (cf. “SELECT COUNT(*) FROM table).


## Optimisation sur le long terme

- Purger les vieilles données non utile permet d’alléger le poids d’une base de données. Idéal pour accélérer la vitesse d’exécution, notamment pour une table contenant beaucoup d’enregistrement
- Une requête OPTIMIZE permet de réorganiser le stockage physique des données et améliore l’efficacité lors de l’accès aux données
- Rappel : consulter les informations recueillies par le fichier “slow queries” pour continuer à détecter si des requêtes peuvent être améliorées. Une requête peut fonctionner très rapidement lorsqu’il n’y a que 1.000 enregistrements, mais peut causer de sérieux problèmes de performance lorsqu’il a plusieurs centaines de milliers de lignes.