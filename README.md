# Checkpoint Java SE/EE 

Dans ce checkpoint, on va valider les compétences web que tu as acquises au cours du dernier mois.

## Objectifs

* Générer un projet web de gestion d'utilisateurs avec Maven sur Eclipse
* Créer la base de données de l'application
* Utiliser JSP, Servlet, JDBC
* Tester ses connaissances théoriques en Java

## Quiz (30 min)

Commence par cloner ce repo.

Crée ta branche Java-0918-[ville]-[prenom]-[nom] (ex: Java-0918-Lyon-John-Smith) dans la quelle tu pusheras tes réponses.

Tu trouveras dans le repo deux PDF contenant des questions sur respectivement Java SE et Java EE
Crée un fichier reponses.txt dans lequel tu noteras tes réponses sans format particulier.

Les questions sont classées par ordre (approximatif) de difficulté. Les questions 20 à 34 du quizz Java EE portent sur des notions qui n'ont pas forcément été vues pendant la formation mais qui pourraient potentiellement vous être posées un jour en entretien ! Ne vous focalisez pas dessus si vous bloquez, elles sont juste là pour permettre de mieux vous évaluer.


## Algorithmie (1h)

Pour tester ton algorithmie sans te soucier des problèmes d'environnement, on utilisera des katas codewars.

FACILE : [Kata modulo](https://www.codewars.com/kata/57f24e6a18e9fad8eb000296)

MOYEN : [Count of positives / sum of negatives](https://www.codewars.com/kata/576bb71bbbcf0951d5000044)

DIFFICILE : [Write number in expanded form](https://www.codewars.com/kata/5842df8ccbd22792a4000245)

[S'IL T'EN FAUT ENCORE](https://www.codewars.com/kata/5541f58a944b85ce6d00006a)

Complète ton *reponses.txt* avec les liens vers tes solutions des katas 

## Projet (2h30 +)

Pour rendre le projet, tu ajouteras le code de ton serveur sur ta branche du repo. La racine de ton serveur est le dossier contenant le *pom.xml*. Pense bien à mettre un .gitignore à jour avant de commit. Pour un projet Maven/ Eclipse Java EE le .gitignore contient :

/.classpath

/.project

/.settings/

/target/

/bin/

Dans ta branche tu devrais avoir à la fin : ton projet + le fichier reponses.txt

## Partie 1 - Mise en place du projet

### Crée le projet via Maven

Sur Eclipse :

- Fais un clic droit dans le *« Project Explorer »* et sélectionnez *New* > *Maven Project* dans le menu contextuel
- Fais *Next*
- Choisissez l'archétype *maven-archetype-webapp*
- Fais *Next*
- Appelle ton projet *gestion-wilder* (artifactid)
- Appuie sur *Finish*

Vérifie que le JRE utilisé par le projet est le 1.8 ou supérieur, sinon modifie le Build Path du projet pour adapter. Le compilateur Maven doit aussi être configuré pour la version 1.8 ou + (dans le pom.xml)

### Crée une JSP

Dans ton dossier *WEB-INF* crée une JSP *listerWilders.jsp* qui se contentera d'afficher un Hello World pour le moment.

### Crée une Servlet

À l'aide d'Eclipse, génère une Servlet *ListerWilders*. Cette servlet devra s'instancier à l'appel de la route */wilder/lister* et rediriger vers la page *listerWilders.jsp* dans le cas d'une requête GET.

Tu peux dès maintenant tester que tu arrives à obtenir ta JSP à l'écran via ton navigateur à l'adresse http://localhost:8080/gestion-wilder/wilder/lister

**ATTENTION : Comme tous les fichiers .java dans un projet Maven (par défaut), ta Servlet devra être placée dans un sous-package de *src.main.java*.** Par exemple dans *src.main.java.com.wcs.gestion-wilder.servlets*. Si ce package n'apparait pas dans ton projet crée-le. La plupart du temps, il est déjà crée mais n'apparait juste pas. Dans ce cas, revois ta version du JRE dans le build path de ton projet.

## Partie 2 - Afficher les données

### Crée une entité

Dans le package *com.wcs.gestion-wilder.models*, crée une classe `Wilder` qui possède les attributs privés suivants :

``` java
matricule (String)
nom (String)
prenom (String)
dateDeNaissance (LocalDate)
Adresse (String)
emailPro (String)
photo (String)
actif (Boolean)
```

Pense à implémenter des getters et des setters.

### Crée un service

Crée une nouvelle classe `WilderService` dans *com.wcs.gestion-wilder.services*. Elle contiendra la méthode *findWilders()* qui retourne une liste de 3 Wilder créés en dur.

Dans ta Servlet, utilise le service en le récupérant grâce à la ligne : 
``` java
WilderService serv = new WilderService();
```

Normalement on devrait utiliser l'injection de dépendances + une seule instance du service à travers l'application (singleton) mais vu le temps qu'on a pour ce checkpoint, on utilisera un import classique avec couplage fort.

### Transmets à la JSP

Ta Servlet doit désormais pouvoir faire appel à *findWilders()* la méthode de ton service pour récupérer des `Wilder`.
En utilisant *request.setAttribute()*, transmets cette liste de Wilder à la JSP *listerWilders.jsp*

Affiche les données de ces wilders à l'intérieur d'un tableau dans la JSP. Ajoute les dépendances CDN Bootstrap pour obtenir quelque chose de joli facilement.

RECOMMANDE : Utilise la jstl 1.2. Tu peux l'importer via Maven. Parfois l'import Maven de la jstl pose problème et donc on préfère l'importer directement en copiant le *jstl-1.2.jar* sur notre serveur dans */WEB-INF/lib* (on pensera bien à ajouter ce jar au build path du projet).

## Partie 3 - Persister les données

### Crée la base de données

Crée une base de données sur MySQL (ou autre base de données que tu sais utiliser) nommée CheckpointJava. 
Crée une unique table User avec les champs adaptés à recevoir les attributs de ton entité User.

### Crée une classe DAO

Crée une classe `WilderDAO` dans le package *com.wcs.gestion-wilder.DAO*
Réalise l'import du connecteur JDBC pour MySQL (ou autre selon ta base) dans ton serveur.

Cette classe `WilderDAO` devra implémenter la méthode getWildersFromDatabase() qui devra à l'aide des méthodes de JDBC :
- créer une connexion (Connection)
- envoyer une requête SELECT sur la table Wilder (Statement, PreparedStatement)
- retourner une liste de Wilder récupérés en base (ResultSet)

Pense à détruire tous ces objets à la fin de la méthode.

### Utilise le DAO dans ton service

Dans ton `WilderService`, au lieu de retourner les 3 `Wilder` créés en dur, fais appel à la méthode *getWildersFromDatabase()* de ton DAO.

Ta JSP doit désormais t'afficher les `Wilder` que tu as en base !

## Partie 4 - Ajouter un Wilder (OPTIONNEL)

Crée `AjouterWilderServlet` qui capte la route */wilder/ajouter*. Sa méthode *doGet()* renvoie sur une jsp *creerWilder.jsp* qui contient un formulaire d'inscription pour un wilder.

Votre formulaire commence avec cette balise 
``` html 
<form method="post" action="/wilder/ajouter"> 
```
Ainsi votre servlet devra implémenter la méthode *doPost()* qui fait persister un `Wilder` en base.

Cette méthode *doPost()* devra donc utiliser une méthode dans votre service WilderService que vous nommerez  *creerWilder(Wilder w)*. Cette méthode fera appel à une méthode *ajouterWilderInDatabase()* codée dans votre fichier `WilderDAO`. 

Une fois cette action effectuée, la méthode doPost doit vous rediriger vers la page *listerWilders.jsp*

# java-lyon-florent-perticoz
# java-lyon-florent-perticoz
