les statistiques sont utilisé pour l’optimisation des données. L'optimiseur de requête va s'en servir pour créer des plan de requête. Pour la plupart des requêtes, l'optimiseur de requête génère déjà les statistiques utiles à un plan de requête de haute qualité ; mais il peut être modifier pour obtenir des résultats optimaux.

Les statistiques sont objets BLOD(binary large object) qui vont contenir des information sur les statistique des donnée d'une table.

# Création des statistiques

### Pre-requis

Il faut vérifier que l'option AUTO_CREATE_STATISTICS est définie au niveau de la base de données. Cela garantit que l'optimiseur de requête continue de créer régulièrement des statistiques de colonnes uniques pour les colonnes de prédicat de requête.
Vous ne pouvez pas supprimer, renommer ni modifier la définition d'une colonne de table définie dans un prédicat de statistiques filtrées.

##Utilisation de SQL Server Management Studio
####Pour créer des statistiques

1. Dans l' Explorateur d'objets, cliquez sur le signe plus (+) pour développer la base de données dans laquelle vous souhaitez créer une nouvelle statistique.

2. Cliquez sur le signe plus (+) pour développer le dossier Tables .

3. Cliquez sur le signe plus (+) pour développer la table dans laquelle vous souhaitez créer une statistique.

4. Cliquez avec le bouton droit sur le dossier Statistiques et sélectionnez Nouvelles statistiques... .

5. Dans la boîte de dialogue Nouvelles statistiques sur la tablenom_table, dans la page Général, cliquez sur Ajouter.

6. Dans la boîte de dialogue Sélectionner les colonnes , activez la ou les cases à cocher de chaque colonne pour laquelle vous voulez créer une statistique, puis cliquez sur OK.

7. Dans la boîte de dialogue Nouvelles statistiques sur la tablenom_table, cliquez sur OK.

## Utilisation de Transact-SQL

1. Dans l' Explorateur d'objets, connectez-vous à une instance du Moteur de base de données.

2. Dans la barre d'outils standard, cliquez sur Nouvelle requête.

3. Exécuter ce code
~~~~sql
CREATE STATISTICS statistics_name   
ON { table_or_indexed_view_name } ( column [ ,...n ] )   
    [ WITH FULLSCAN ] ;  

~~~~

Il devra améliorer les résultat de la requête qui lui est associé. 
https://docs.microsoft.com/fr-fr/sql/t-sql/statements/create-statistics-transact-sql?view=sql-server-ver15