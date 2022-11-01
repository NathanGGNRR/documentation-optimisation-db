#Qu'est ce qu'un plan d'exécution?

Un plan d'exécution est la manière la plus efficace d'accéder aux données, qui doit étre déterminé par le moniteur de base de données SQL Server.
 Cette analyse est gérée par un composant appelé Optimiseur de requête. L’entrée de l’optimiseur de requête est composée de la requête, du schéma de base de données et de ses statistiques de base de données. La sortie de l’optimiseur de requête est un plan d’exécution de requête.

Il va nous permettre de définir:
- L’ordre d’accès aux tables sources.
- Les méthodes utilisées pour extraire les données des différentes tables
- Les méthodes utilisées pour effectuer les calculs, et filtrer, agréger et trier les données des différentes tables

L’Optimiseur de requête de SQL Server ne génère qu’un seul plan d’exécution, il existe le concept de plan d’exécution estimé et de plan d’exécution réel.

- Un plan d’exécution estimé retourne le plan d’exécution produit par l’Optimiseur de requête au moment de la compilation. Le fait de produire le plan d’exécution estimé n’exécute pas réellement la requête ou le lot, et par conséquent ne contient aucune information d’exécution, comme des avertissements d’exécution ou des métriques d’utilisation des ressources réelles.
- Un plan d’exécution réel retourne le plan d’exécution produit par l’Optimiseur de requête, et une fois que les requêtes ou les lots ont été exécutés. Cela inclut les informations d’exécution concernant les métriques d’utilisation des ressources et les avertissements d’exécution.

# Affichage et enregistrer du plan d'exécution 
~~~~sql
SET SHOWPLAN_ALL { ON | OFF }
~~~~

Si SET SHOWPLAN_ALL a la valeur ON, SQL Server retourne des informations sur l’exécution de chaque instruction sans toutefois l’exécuter ; les instructions Transact-SQL ne sont pas exécutées. Une fois cette option activée, des informations sur toutes les instructions Transact-SQL ultérieures sont retournées, jusqu'à ce que l'option soit de nouveau désactivée. 

## Affichage du plan d'exécution estimé
https://docs.microsoft.com/fr-fr/sql/relational-databases/performance/display-the-estimated-execution-plan?view=sql-server-ver15

## Afficher un plan d'exécution réel
https://docs.microsoft.com/fr-fr/sql/relational-databases/performance/display-an-actual-execution-plan?view=sql-server-ver15

# Afficher le plan d'exécution via Sql

On peut afficher le plan d'exécution via SQL en utilisant  l’instruction EXPLAIN juste avant un SELECT. Cela permet de savoir de quelle manière le Système de Gestion de Base de Données (SGBD) va exécuter la requête et s’il va utiliser des index et lesquels.