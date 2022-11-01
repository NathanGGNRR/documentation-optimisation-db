# 1. Qu'est ce que ADO?

- ADO est un ensemble de classe qui vont nous permettre de nous connecter à la base de donnée comme SQL Server et XML et nous propose des composants pour la création d'applications distribuées avec partage de données.

- Il sépare l'accès aux données de leur manipulation en composants distincts qui peuvent être utilisés individuellement ou en tandem.

- ADO comprend des fournisseurs de données .NET Framework pour la connexion à une base de données, l'exécution de commandes et l'extraction de résultats. Ces résultats sont traités directement, placés dans un objet DataSet ADO.NET pour pouvoir être exposés à l'utilisateur, combinés aux données de différentes sources ou passées entre couches.

 # 2. ARCHITECTURE

Son architecture repose sur un modèle à deux couches utilisant une connexion. Les deux composants d'ADO sont le fournisseur de donnée et le Dataset.

1. Fournisseur de données:

Les fournisseurs de données .NET Framework sont des composants qui ont été conçus pour la manipulation des données et pour un accès aux données rapide. 
- L'objet Connection assure la connectivité avec une source de données. 
- L'objet Command permet d'accéder aux commandes de base de données pour retourner, de modifier des données, d'exécuter des procédures stockées et d'envoyer ou récupérer des informations sur les paramètres.
- Le DataReader fournit un flux très performant de données en provenance de la source de données.
- le DataAdapter établit une passerelle entre l'objet DataSet et la source de données. Le DataAdapter utilise des objets Command pour exécuter des commandes SQL au niveau de la source de données pour charger le DataSet avec des données, etpour répercuter dans la source de données les modifications apportées aux données contenues dans le DataSet.

2.Dataset:

Le DataSet est conçu pour un accès aux données indépendant de toute source de données. Il peut donc être utilisé avec plusieurs sources de données différentes, utilisé avec des données XML ou utilisé pour gérer des données locales de l'application.
Le DataSet contient une collection d’un ou plusieurs objets DataTable qui va contenir des données, ainsi que des informations concernant les contraintes de clé primaire, de clé étrangère et des informations relationnelles sur les données contenues dans les objets DataTable.

Un objet DataView vous permet de créer différentes vues des données stockées dans un objet DataTable, possibilité qui est souvent utilisée dans les applications de liaison de données. En utilisant un DataView, vous pouvez présenter les données d'une table en appliquant différents ordres de tri et filtrer les données en fonction d'un état de ligne ou d'une expression de filtre. Pour plus d’informations, consultez les DataView. 

Dans le DataSet, les relations sont contenu dans le DataRelationCollection objet. Une relation, représentée par le DataRelation objet, qui va associe les lignes d’un DataTable aux lignes d’une autre DataTable. Un DataRelation identifie les colonnes correspondantes dans deux tables d’un DataSet. Les relations permettent de naviguer d’une table à l’autre dans un jeu de données. Les éléments essentiels d’un DataRelation sont le nom de la relation, le nom des tables en relation et les colonnes associées dans chaque table. Une relation peut être générée avec plusieurs colonnes par table en spécifiant un tableau d'objets DataColumn en tant que colonnes clés. Lorsque vous ajoutez une relation au DataRelationCollection , vous pouvez éventuellement ajouter un UniqueKeyConstraint et un ForeignKeyConstraint pour appliquer des contraintes d’intégrité quand des modifications sont apportées à des valeurs de colonne associées.

Le diagramme de la relation entre un fournisseur de données .NET Framework et un DataSet

![ado.png](/.attachments/ado-00fd5bf8-161a-46bb-ac55-3b60e912831f.png)

Quand choisir Dataset:

On choisie d’utiliser le Dataset quand notre application a besoin effectuer les opérations suivantes :

- Mettre des données en cache localement dans votre application afin de pouvoir les manipuler.
- Fournir un accès distant entre couches ou à partir d'un service Web XML.
- Interagir dynamiquement avec les données par le biais d'un formulaire contrôle Windows Forms ou de la combinaison et la mise en relation de données de diverses sources.
- Réaliser un traitement complet des données sans qu'une connexion ouverte à la source de données soit nécessaire, ce qui libère la connexion pour d'autres clients.

Si vous n'avez pas besoin ces fonctionnalités, vous pouvez améliorer les performances de votre application en utilisant le DataReader pour retourner les données avec un accès en lecture seule et avant uniquement. Vous améliorez les performances, car vous économiserez de la mémoire qui serait consommée par le DataSet , et évitez le traitement requis pour créer et remplir le contenu de DataSet .

# 3.  Application

Pour mettre en place ADO il faut:

1. Créer une table SQL
2. Créer un projet d'API Web ASP.NET Core
3. Créer un dossier et ajouter une classe
Il faut maintenant créer un dossier nommé DB. Le dossier DB aura une classe nommée Database, qui aura une variable sqlDataSource.
Il faut créer une chaîne de connexion et une fonction GetData, qui sera utilisée pour récupérer les données de la base de données en tant que DataTable.
Il y aura fonction ExecuteData, utilisée pour insérer des données dans la base de données. 

4. Effectuer des opérations de base de données
5. Tester l'API à l'aide de Postman

Exemple: https://www.c-sharpcorner.com/article/database-operations-in-asp-net-core-web-api-using-ado-net2/ 


Source: https://docs.microsoft.com/fr-fr/dotnet/framework/data/adonet/security-overview