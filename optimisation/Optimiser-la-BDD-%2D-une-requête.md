# La BDD :

Dans un premier temps, l'analyse de la base de données. Sur SQL Server Management Studio, allez dans _"Outils > Assistant Paramétrage du moteur de base de données"_
![image.png](/.attachments/image-dc34de2b-86c2-42c9-b266-803c29f01981.png)

Connectez-vous au serveur de la base de données. Puis sélectionnez la base de données que vous souhaitez analyser.
![image.png](/.attachments/image-4134a2be-e516-43e5-8a85-ceb431e22b97.png)

Puis "Options de paramétrage" et selectionnez les paramètres comme sur l'image.
![image.png](/.attachments/image-4b3f557f-afc5-4822-9aad-7417272df197.png)

Enfin appuyez sur démarrer l'analyse.
![image.png](/.attachments/image-cfcbbfbe-6f37-49da-8cbd-2cb6afecf67c.png)

Une fois l'analyse terminé, un nombre d'action à réaliser sera recommandé.
![image.png](/.attachments/image-3bca7d99-9234-4d28-b094-587d6d91cd16.png)

Comme on peut le voir, il y a plusieurs améliorations avec une estimation d'amélioration de la base de données.
Exécutez par la suite les recommandations :
![image.png](/.attachments/image-950bfd29-c5f7-4b6e-a493-ec4b3449037d.png)

Pour être sûr des modifications, relancé le programme, réeffectuez l'analyse et en toute logique il ne devrait plus y avoir de recommandations.
Cette solution ne fonctionne qu'une seule fois mais permet d'améliorer considérablement les manques dans notre base de données.


# Une requête :
## Le commencement :

Dans un premier temps, choisir son modèle de conception de la requête.
Généralement, deux choix s'offrent à vous :
- Le design vue imbriquées, imbrication de vues où une vue correspond à une requête.
- Un seule requête avec CTE, imbrication d'autres requêtes directement dans la requête principale.


## Design imbrication de vue :
Voici un exemple d'une requête en vue imbriquées:

```
CREATE VIEW SalesNumberByMonth WITH SCHEMABINDING
AS
SELECT DATENAME(MONTH, [OH].[DueDate]) AS [BestMonth], SUM([OD].[OrderQty]) AS [SalesNumber]
FROM [dbo].[OrderDetail] AS [OD]
INNER JOIN [dbo].[OrderHeader] AS OH ON [OH].[SalesOrderID] = [OD].[OrderID]
WHERE [OH].[DueDate] BETWEEN DATEADD(year, -1, GETDATE()) and GETDATE()
GROUP BY DATENAME(MONTH, [OH].[DueDate])
GO

CREATE VIEW BestSalesNumberByMonth WITH SCHEMABINDING
AS
SELECT MAX([SNBM].[SalesNumber]) AS [MaxSalesNumber] 
FROM [dbo].[SalesNumberByMonth] AS [SNBM]
GO

CREATE VIEW BestMonth WITH SCHEMABINDING
AS
SELECT STRING_AGG([SNBM].[BestMonth], ', ') AS [BestMonth], [SNBM].[SalesNumber] AS [SalesNumber]
FROM [dbo].[SalesNumberByMonth] AS [SNBM], [dbo].[BestSalesNumberByMonth] AS [BSNBM]
WHERE [SNBM].[SalesNumber] = [BSNBM].[MaxSalesNumber]
GROUP BY [SNBM].[SalesNumber]
GO

SELECT *
FROM BestMonth
```
Chaque requêtes est englobées dans une vue et une vue peu en appelée une autre pour récupérer le résultat.
### Point fort :
**- Meilleur lisibilité**
**- Peut être réutilisé dans d'autre requête**
**- Plus facile d'optimiser mais moins optimisable (coût pour reprendre la vue car plus d'appels, optimisable requête par requête)**
**- Fait abstraction de la structure de la base de données (la vue peut être considéré comme une nouvelle table)**

### Point faible :
**- Plus compliqué pour si retrouver (chercher la vue adéquate et savoir ce qu'elle retourne)**
**- Moins optimisable car plus d'appel à la base de données**

## Design imbrication de requête :

```
WITH [SalesNumberForTrainings]
AS
(
	SELECT [T].[TrainingID] as [TTrainingID], SUM([OD].[OrderQty]) as [SalesNumber]
	FROM [dbo].[Training] AS [T]
	INNER JOIN [dbo].[Product] AS [P] ON [P].[TrainingID] = [T].[TrainingID]
	INNER JOIN [dbo].[OrderDetail] AS [OD] ON [OD].[ProductID] = [P].[ProductID]
	INNER JOIN [dbo].[OrderHeader] AS [OH] ON [OH].[SalesOrderID] = [OD].[OrderID]
	WHERE [OH].[DueDate] BETWEEN DATEADD(year, -1, GETDATE()) and GETDATE()
	GROUP BY [T].[TrainingID]
)
SELECT [SNFT].[TTrainingID] AS [TTrainingID], [SNFT].[SalesNumber] AS [SalesNumber]
FROM [SalesNumberForTrainings] AS [SNFT]
GROUP BY [SNFT].[TTrainingID], [SNFT].[SalesNumber]
HAVING [SNFT].[SalesNumber] > (SELECT AVG([SNFT].[SalesNumber]) FROM [SalesNumberForTrainings] AS [SNFT])
```
Chaque requête demandé par le client sera composé d'une seule requête. Il faut savoir que ce modèle est le plus optimisé de base mais le plus compliqué à mettre en place et surtout beaucoup plus compliqué à optimisé.

### Point fort :
**- Optimisé de base.**
**- Une seule requête, pas besoin d'aller à droite à gauche.**

### Point faible :
- **Ne peut pas être mise en place dans tous les cas.**
- **Très peu lisible si grand nombre d'imbrication.**
- **Plus compliqué à optimiser (par exemple, une CTE ne peut pas être indexée)**
- **Si l'on souhaite refaire la même chose en inversant une condition, toute la requête doit être recopiée.**

Pour résumer, le choix doit se faire en fonction de ce que vous souhaitez, si la requête n'est pas trop compliqué il est préférable de faire une requête imbriqué, sinon il sera largement préférable de faire plusieurs vues.
Après c'est à vous de décider, dans mon cas personnel j'ai privilégié la création de vue pour traiter les requêtes plus rapidement et en préférant optimiser vue par vue.

Dans le cas où vous préféré vous aussi utiliser les vues, voici une partie sur la création de vue indexées plus bas après l'analyse des données.

## Analyse des données :
Pour être sûr de l'optimisation des requêtes, il faut mettre en place de outils d'analyse.
Le plan d'exécution et l'activation des statistiques.
### Le plan d'exécution :
Très simple, il suffit de cocher ces petites cases dans SQL Server Management Studio pour afficher l'onglet souhaité.
![image.png](/.attachments/image-3c05031b-4b31-4b74-b8ec-49511817829f.png)
Par la suite, dès que vous exécuterez ou analyserez la requête, il y aura trois onglets dans le résultat :
![image.png](/.attachments/image-a5c96a4a-4019-4e34-8a52-27d33a42ec18.png)
Sachant que la statistique client est optionnel et peut être décoché si vous ne le souhaitez pas.

### L'activation des statistiques :
Cet activation se fait par requête et non dans la base de données (pas sûr mais pas trouvé autrement).
Pour chaque requête, il faut avant d'effectuer le SELECT mettre :

```
SET STATISTICS IO ON
GO
```
et finir par
`GO`
ce qui donne :

```
SET STATISTICS IO ON
GO
SELECT *
FROM BestMonthForTraining
GO
```
Cette ajout permet de voir dans qu'elle table nous passons et combien de lignes sont lues, traitées.
![image.png](/.attachments/image-2253b68c-3bac-4605-a07e-3ff3c7892a13.png)

Le but, vous l'aurez compris est de diminuer au maximum ces informations et de mettre en place les recommandations du plan d'exécution.

En ce qui concerne la plan d'exécution, il vous indiquera avec des icônes de faire attention à un point en particuliers avec une possibilité d'améliorations ou une erreur qui va vous permettre d'améliorer les requêtes et de vous guider pour mieux optimiser ces requêtes.

## Création d'une vue indexées :
Avant de créer la vue indexées il faut d'abord savoir si c'est pertinent et possible de le créer.
Voici les 5 étapes :
1. Vérifiez que les options SET sont correctes pour toutes les tables existantes qui seront référencées dans la vue. (**cette vérification est dans la majorité des cas toujours bonnes**).
2. Vérifiez que les options SET de la session sont définies correctement avant de créer des tables et la vue. (**cette vérification est dans la majorité des cas toujours bonnes**)
3. Vérifiez que la définition de la vue est déterministe. Cela signifie que la colonne où vous souhaitez ajouté l'index doit être unique et est déterminante. Pour plus d'information rendez-vous ici: [**How to determine if my column is deterministic**](https://mostafaelmasry.com/2013/05/17/how-can-i-create-index-on-non-deterministic-column-in-sql-server/)
4. Créez la vue en utilisant l’option WITH SCHEMABINDING. Attention, il est impératif d'utiliser cette notation :
- `[dbo].[NomDeLaTable] AS [ALIAS]` pour l'appel à une table (avec ou sans crochet)
- `[ALIAS].[NomDeLaColonne]` pour l'appel à une colonne
- Toujours ajouter des alias pour facilité la lisibilité.
5. Créez l'index cluster unique sur la vue.

## Pistes d'amélioration de performance:
Voici un exemple d'une requête optimisé:

```
CREATE VIEW SalesNumberByMonthForTraining
WITH SCHEMABINDING
AS
SELECT [T].[TrainingID] as [TTrainingID], MONTH([OH].[DueDate]) as [BestMonth], SUM([OD].[OrderQty]) as [SalesNumber], COUNT_BIG(*) as tmp
FROM [dbo].[Training] AS [T]
INNER JOIN [dbo].[Product] AS [P] ON [P].[TrainingID] = [T].[TrainingID]
INNER JOIN [dbo].[OrderDetail] AS [OD] ON [OD].[ProductID] = [P].[ProductID]
INNER JOIN [dbo].[OrderHeader] AS [OH] ON [OH].[SalesOrderID] = [OD].[OrderID]
GROUP BY [T].[TrainingID], MONTH([OH].[DueDate])

CREATE UNIQUE CLUSTERED INDEX 
    [IDX_SalesNumberByMonthForTraining_TTrainingID] 
ON [dbo].[SalesNumberByMonthForTraining]([TTrainingID], [BestMonth]);


CREATE VIEW SalesMaxNumberByMonthForTraining
WITH SCHEMABINDING
AS 
SELECT [SNBMFT].[TTrainingID] AS [TTrainingID], MAX([SNBMFT].[SalesNumber]) AS [MaxSalesNumber]
FROM [dbo].[SalesNumberByMonthForTraining] AS [SNBMFT]
GROUP BY [SNBMFT].[TTrainingID]


CREATE VIEW BestMonthForTraining
WITH SCHEMABINDING
AS
SELECT [SNBMFT].[TTrainingID], STRING_AGG(CONVERT(char, [SNBMFT].[BestMonth]), ', ') AS [BestMonth], [SNBMFT].[SalesNumber] AS [SalesNumber]
FROM [dbo].[SalesNumberByMonthForTraining] AS [SNBMFT]
INNER MERGE JOIN [dbo].[SalesMaxNumberByMonthForTraining] AS [SMNBMFT] ON [SNBMFT].[TTrainingID] = [SMNBMFT].[TTrainingID]
WHERE [SNBMFT].[SalesNumber] = [SMNBMFT].[MaxSalesNumber]
GROUP BY [SNBMFT].[TTrainingID], [SNBMFT].[SalesNumber]


SET STATISTICS TIME ON
SET STATISTICS IO ON
GO
SELECT *
FROM BestMonthForTraining
OPTION(FAST 1)
GO
SET STATISTICS IO OFF
SET STATISTICS TIME OFF
```
Dans un premier temps, je fait ma requête avec les différentes vues pour avoir le résultat attendue. 
Deuxièmement, je rajoute SCHEMABINDING dans mes vues.
Ensuite, je rajoute des index où cela est nécessaire : dans toutes les view où il y a une colonne déterminante (ID le plus souvent). Dans mon exemple je rajoute unique un index clustered dans la vue _SalesNumberByMonthForTraining_.
**N'oubliez pas de rajouter le champ COUNT_BIG(*) AS tmp pour que l'indexation fonctionne.**

```
CREATE VIEW SalesNumberByMonthForTraining
WITH SCHEMABINDING
AS
SELECT [T].[TrainingID] as [TTrainingID], MONTH([OH].[DueDate]) as [BestMonth], SUM([OD].[OrderQty]) as [SalesNumber], COUNT_BIG(*) as tmp
FROM [dbo].[Training] AS [T]
INNER JOIN [dbo].[Product] AS [P] ON [P].[TrainingID] = [T].[TrainingID]
INNER JOIN [dbo].[OrderDetail] AS [OD] ON [OD].[ProductID] = [P].[ProductID]
INNER JOIN [dbo].[OrderHeader] AS [OH] ON [OH].[SalesOrderID] = [OD].[OrderID]
GROUP BY [T].[TrainingID], MONTH([OH].[DueDate])

CREATE UNIQUE CLUSTERED INDEX 
    [IDX_SalesNumberByMonthForTraining_TTrainingID] 
ON [dbo].[SalesNumberByMonthForTraining]([TTrainingID], [BestMonth]);
```
Cependant, étant donné qu'un index doit être unique, je créé l'index sur deux valeurs : l'identifiant de la formation et le mois de vente.

**ATTENTION** : A partir du moment, où dans la requête, il y a une fonction non déterminante, l'indexation sera impossible même si cette fonction n'est pas dans les champs sélectionnés. La plupart des fonction d'agrégations ne fonctionneront pas. De plus si l'indexation se fait sur un colonne non déterminante, il y aura une erreur.


Ensuite, l'utilisation d'opération de jointure, il en existe trois:
- LOOP
- HASH
- MERGE
Ce sont des opérations qui permettent de définir comment sera crée la jointure. Dans certains cas, ces opérations accélère le traitement des jointures. Cet ajout est à tester pour voir si il y a un gain de performance.
Dans notre exemple j'utilise MERGE à un seul endroit: la troisième vue :

```
CREATE VIEW BestMonthForTraining
WITH SCHEMABINDING
AS
SELECT [SNBMFT].[TTrainingID], STRING_AGG(CONVERT(char, [SNBMFT].[BestMonth]), ', ') AS [BestMonth], [SNBMFT].[SalesNumber] AS [SalesNumber]
FROM [dbo].[SalesNumberByMonthForTraining] AS [SNBMFT]
INNER MERGE JOIN [dbo].[SalesMaxNumberByMonthForTraining] AS [SMNBMFT] ON [SNBMFT].[TTrainingID] = [SMNBMFT].[TTrainingID]
WHERE [SNBMFT].[SalesNumber] = [SMNBMFT].[MaxSalesNumber]
GROUP BY [SNBMFT].[TTrainingID], [SNBMFT].[SalesNumber]
```
Ces opérations de jointures ne peuvent être ajouté uniquement sur des vues qui ne sont pas indexées. Si vous souhaitez indexée une vue assurée vous qu'elle ne contienne aucune opération de jointure. 

Enfin, l'`OPTION(RECOMPILE, FAST 1)` ne fonctionne uniquement sur des requêtes qui ne sont pas dans une vue, donc ce paramètre doit être ajouté à la reprise de la vue qui renvoie tous les résultats. Plus d'info en lien en bas de page concernant cette option.
Pour ce qui est du RECOMPILE, cela permet de régénérer le plan d'exécution.

## Tester et conclure:
Pour tester efficacement, il faut activer le plan d'exécution, activé les statistiques sur la requêtes :

```
SET STATISTICS TIME ON
SET STATISTICS IO ON
GO
SELECT *
FROM BestMonthForTraining
OPTION(FAST 1)
GO
SET STATISTICS IO OFF
SET STATISTICS TIME OFF
```
Cette requête va nous retourner les résultats, le temps d'exécution et un plan d'exécution et va vous permettre de conclure sur les différents changements qui ont été fait.
![image.png](/.attachments/image-7fbd4dd4-86c6-4dcf-8782-810d0f653ffe.png)

Pour mon exemple voici un comparatif des différents temps avec les différentes options évoquées précédemment :

```
Query 3/5-3

Without INDEX (AVG):
CPU time = 79ms, elapsed time = 80ms

With INDEX (AVG):
CPU time = 20ms, elapsed time = 25ms
	With MERGE (AVG):
		CPU Time = 14ms, elapsed time = 13ms
		With OPTION(FAST 1):
			CPU Time = 7ms, elapsed time = 12ms
			With RECOMPILE:
				CPU Time = 13ms, elapsed time = 12 ms
			Without RECOMPILE:
				CPU Time = 7 ms, elapsed time = 12ms

		With OPTION(FAST 100):
			CPU Time = 9ms, elapsed time = 12ms
	With HASH (AVG):
		CPU Time = 15ms, elapsed time = 15ms
	With LOOP (AVG):
                CPU Time = 8257ms, elapsed time = 8420ms
```
Avec ces améliorations, nous avons un gain de 60 ms environs. Ce qui n'est pas rien sur 100 000 données de test.


# Liens utiles :
Voici quelques liens utiles sur des erreurs potentiellement rencontrées :
[**Names must be in two-part format and an object cannot reference itself**](http://www.sql-server-helper.com/error-messages/msg-4512.aspx) (schema binding incorrecte, utilisation la notation)
[**It does not have a unique clustered index**](http://www.sql-server-helper.com/error-messages/msg-1940.aspx) (pour créer un index non clustered, il faut d'abord avoir un index clustered)
[**Non deterministic values**](https://blog.sqlauthority.com/2009/12/30/sql-server-fix-error-msg-1949-level-16-cannot-create-index-on-view-the-function-yields-nondeterministic-results-use-a-deterministic-system-function-or-modify-the-user-defined-function-to-r/) (A partir du moment, où dans une vue, il y a une fonction non déterminante, la vue ne peux pas être indexée)

Voici un tutoriel bien expliqué sur la création d'une vue indexée sur SQL Server : 
[**How to index view**](https://www.sqlservertutorial.net/sql-server-views/sql-server-indexed-view/)


Encore plus de lien qui m'ont aidé à faire ce wiki :
[**Plusieurs proposition d'optimisation de nos requêtes**](https://www.sqlshack.com/query-optimization-techniques-in-sql-server-tips-and-tricks/)
[**Mettre en place le plan d'exécution et correctement le lire**](https://www.sqlshack.com/sql-server-query-execution-plans-viewing-plans/)
[**Utilisation de OPTION(FAST N)**](https://blog.sqlauthority.com/2020/02/11/sql-server-optionfast-n-hint-and-performance/)
[**Indicateurs de requête**](https://docs.microsoft.com/fr-fr/sql/t-sql/queries/hints-transact-sql-query?view=sql-server-ver15)
