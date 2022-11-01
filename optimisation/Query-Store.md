« Query Store » est une nouvelle fonctionnalité de base de données. L’une de ses principales fonctions est de stocker les différents plans et temps d’exécution générés pour une même requête. L’analyse des performances est ainsi simplifiée.

# Activation du magasin de requêtes

Le Query Store est activé par défaut pour les nouvelles bases de données Azure SQL Database, mais non pour les nouvelles bases de données SQL Server et Azure Synapse Analytics.

### Utiliser la page Magasin des requêtes dans SQL Server Management Studio

1. Dans l’Explorateur d’objets, cliquez avec le bouton droit sur une base de données, puis sur Propriétés.

2. Dans la boîte de dialogue Propriétés de la base de données , sélectionnez la page Magasin de requêtes .

3. Dans la zone Mode d’opération (demandé) , sélectionnez Lecture Écriture.

### Utilisation d’instructions Transact-SQL

Utilisez l’instruction ALTER DATABASE pour activer le Magasin des requêtes pour une base de données déterminée. Par exemple :

~~~~sql
SET QUERY_STORE = ON (OPERATION_MODE = READ_WRITE);
~~~~

Plus d'information :
https://docs.microsoft.com/fr-fr/sql/t-sql/statements/alter-database-transact-sql-set-options?view=sql-server-ver15