# Qu'est ce qu'un service?

Un service est un objet qui obéit au pattern singleton et qui sert à effectuer des tâches spécifiques; c'est dans les différents services qu'on doit trouver la logique métier d'une application Angular.

Angular sépare les composants des services pour augmenter la modularité et la ré-utilisabilité. En séparant les fonctionnalités liées à la vue d'un composant des autres types de traitement, vous pouvez rendre vos classes de composants allégées et efficaces

Un service permet de centraliser des parties de votre code et des données qui sont utilisées par plusieurs parties de votre application, il permet donc : 

-  de ne pas avoir le même code doublé ou triplé à différents niveaux de l'application - ça facilite donc la maintenance, la lisibilité et la stabilité du code

-  de ne pas copier inutilement des données - si tout est centralisé, chaque partie de l'application aura accès aux mêmes informations, évitant beaucoup d'erreurs potentielles. 

Un composant peut déléguer certaines tâches à des services, telles que la récupération de données à partir du serveur, la validation des entrées utilisateur ou la journalisation directement dans la console.

Angular embarque plusieurs services déjà prédéfini, mais il est possible d'en créer d'autre.


# Création d'un service

Tous d'abord créer un répertoire services.

Pour la création on va utiliser la commande :
~~~~csharp
 ng g s services\nom_du_service
~~~~

#Déclaration d'un service

Pour déclarer un service Angular, il suffit de créer une classe TypeScript et de la décorer avec le décorateur @Injectable().

~~~~csharp
@Injectable()
export class DataService {
    ...
}
~~~~

#Définition d'un Provider

Pour instancier un service, Angular a besoin d'un "provider" lui indiquant comment produire l'instance de ce service.


~~~~csharp
@NgModule({
    providers: [
        {
            provider: DataService,
        }
    ]
})
export class AppModule {
}
~~~~
