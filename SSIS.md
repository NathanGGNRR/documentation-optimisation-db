# Wiki SSIS

SQL Server Integration Services est une plateforme qui permet de générer des solutions de transformation et d’intégration de données au niveau de l’entreprise. Utilisez Integration Services pour résoudre des problèmes métier complexes en copiant ou en téléchargeant des fichiers, en chargeant des entrepôts de données, en nettoyant et en explorant des données et en gérant des données et des objets SQL Server.
Integration Services peut extraire et transformer des données provenant d’une grande variété de sources, par exemple des fichiers de données XML, des fichiers plats et des sources de données relationnelles, puis charger les données dans une ou plusieurs destinations.
Integration Services offre un large éventail de tâches et de transformations prédéfinies, des outils graphiques pour créer des packages et la base de données du catalogue Integration Services permettant de stocker, d’exécuter et de gérer les packages.
Vous pouvez utiliser les outils Integration Services graphiques pour créer des solutions sans écrire une seule ligne de code. Vous avez également la possibilité de programmer le modèle objet Integration Services complet afin de créer des packages par programme et de coder des tâches personnalisées et d’autres objets de package.

<u>Source :</u> [Documentation Microsoft](https://docs.microsoft.com/fr-fr/sql/integration-services/sql-server-integration-services?view=sql-server-ver15)

## Utilité dans notre projet

L'utilité d'SSIS dans notre projet est d'importer les données générées en CSV depuis l'application de génération.
Tout un workflow d'import se déclenche, afin d'importer dans l'ordre les fichiers correspondant aux tables, en maintenant les liens.

## Pré-requis

Avant d'utiliser SSIS, vous devez vérifier que votre installation de SQL Server comporte les composants nécessaires, ainsi que votre Visual Studio dispose de l'extension "SQL Server Integration Services Projects
".

![Composants SQL Server](https://docs.microsoft.com/fr-fr/sql/integration-services/install-windows/install-integration-services/install-integration-services-sql-setup.png?view=sql-server-ver15)

![image.png](/.attachments/image-aeab93b5-342b-4139-b842-6ab081e451b7.png)

## Utilisation

Pour un tutoriel précis concernant l'utilisation de SSIS pour l'import de données, ce référer directement au cours de M. ROMAIN #130

## Implémentation dans notre projet

Voici l'implémentation d'SSIS dans notre projet.

### Connexion à la base de données

![image.png](/.attachments/image-0333be68-38b7-43ef-9614-38ebefabd5fb.png)

La connexion à la base de données s'effectue dans cet objet. Celui-ci contient le fournisseur de connexion (ici, fournisseurs .NET\SqlClient Data Provider), ainsi que les identifiants de connexion, et la base de données dans laquelle injecter les données.

### Workflow

![image.png](/.attachments/image-017292d0-e0a0-413c-a9ae-2b5b5332a5a1.png)

La visualisation du workflow est accessible dans l'onglet "Flux de contrôle". Ici, on peux voir la suite d'instruction qui est exécutée. Elle correspond notamment à l'insertion des données dans l'ordre, de manière à conserver les liens entre les tables. Dans le cadre de notre projet, chaque tâche d'import de données ici est identique, mis à part le fichier source et la table de destination

### Détail d'une tâche d'import de données

Voici le détail d'une tâche d'import de données :

![image.png](/.attachments/image-9f2fcdac-2597-450c-93e6-4dd43ef269cf.png)

Deux tâches sont présentes : une tâche d'import du fichier source, et une tâche d'envoi vers une BDD compatible ADO.NET.

![image.png](/.attachments/image-d22785b3-c3ca-4908-b92b-49e263698e6b.png)

Vous pouvez notamment en modifiant une des tâches, modifier les liens entre les colonnes externes (fichier plat) et les colonnes en table.

Vous trouverez tout les détails sur les documentations officielles de Microsoft.