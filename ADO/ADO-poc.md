Ce poc a pour but de nous permettre de tester ADO.net.
Nous allons mettre en place la connexion à la base de données ainsi que la récupération et intersession de données.

#LA CONNEXION
 
On va créer une variable qui va contenir les information de connexion tel que:
- l'identifiant du serveur
- le nom de la base de données
- le integrated security qui va gérer la sécurité de la connexion. Il peut prendre comme valeur "true" ou "SSPI" donc c'est  une authentification windows, s'il est a false il faut indiquer un nom d'utilisateur et mot de passe qui permette de se connecter à la  base de données.

~~~~csharp
string strConnexion = "Data Source=DESKTOP-V8MRGIC\\ASMASQL; Integrated Security=SSPI;" + "Initial Catalog=testADO";
~~~~

On va créer un objet SqlConnection. Cette classe va hériter de la classe DBConnection

~~~~csharp
            try
            {
                SqlConnection oConnection = new SqlConnection(strConnexion);
                oConnection.Open();
                Console.WriteLine("Etat de la connexion : " + oConnection.State);
                //Insertion(oConnection);
                Recuperer(oConnection);
                //Console.WriteLine("ligne ajouté");
                oConnection.Close();
            }
            catch (Exception e)
            {
                Console.WriteLine("L'erreur suivante a été rencontrée :" + e.Message);
            }    
~~~~~

# La Récupération de données
Pour la récupération de donnée, on va initialiser une variable de type string qui va contenir la requête Sql.

On va initialiser un objet SqlCommand, cette classe va hérité de la classe DBCommand. Cette objet va prendre en attribut une requête et une connexion et va stocker le résultat de se couple.

L'objet SqlDataReader va nous permettre de mettre les données dans un tableau.
Le DataReader est un bon choix lorsque vous récupérez de grandes quantités de données, car les données ne sont pas mises en cache dans la mémoire. Toutefois, pour de meilleures performances, le DataReader fournit une série de méthodes qui vous permettent d’accéder aux valeurs de colonne dans leurs types de données natifs (GetDateTime, GetDouble, GetGuid, GetInt32, etc.). 

~~~~csharp
static void Recuperer(SqlConnection oConnection)
{
    string strRequete = "SELECT name, age FROM [testADO].[dbo].[Abonnes];";
    try
    {
        SqlCommand oCommand = new SqlCommand(strRequete, oConnection);
        SqlDataReader oReader = oCommand.ExecuteReader();
        while (oReader.Read())
        {
           Console.WriteLine(String.Format("{0}{1}", oReader[0], oReader[1]));
        }
        oReader.Close();
    }
    catch (Exception e)
    {
        Console.WriteLine("L'erreur suivante a été rencontrée :" + e.Message);
    }  
}

~~~~

# L'insertion des données

On va utiliser une requête d'insertion. On utiliser l'objet SqlConnection qui va lanser la connexion et envoyer la requête.

~~~~csharp
static void Insertion(SqlConnection oConnection)
{
    string strRequete = "INSERT INTO [testADO].[dbo].[Abonnes] VALUES ('afaf',23)";
    try
    {
        SqlCommand oCommand = new SqlCommand(strRequete, oConnection);
        oCommand.ExecuteReader();
    }
    catch (Exception e)
    {
        Console.WriteLine("L'erreur suivante a été rencontrée :" + e.Message);
    }
}
~~~~


