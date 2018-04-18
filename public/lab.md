# DevoxxFR 2018 : Angular
## Hands-on Lab : initiation à Angular 6 par la pratique

## Outils nécessaires

* NodeJS, version 8 ou 9
* Git
* IDE : WebStorm (démo 30 jours, recommandé) ou Visual Studio Code

## Installation du workshop

* Angular-CLI **version 6.0.0-rc.5** (après avoir désinstallé une éventuelle 
version antérieure) :
```
npm uninstall -g @angular/cli
npm install -g @angular/cli@6.0.0-rc.5 
```

* Cloner le projet mars-api et démarrer le serveur d'API :
```
git clone https://github.com/tchatel/mars-api.git
cd mars-api
npm install
npm run api 
```
Vous devez maintenant pouvoir afficher cet énoncé à l'adresse `http://localhost:3390/`


* Télécharger et décompresser le fichier suivant (présent sur le serveur d'API), contenant surtout 
des templates HTML et des styles CSS fournis pour le workshop :
```
http://localhost:3390/mars-files.zip
```

* Créer un nouveau projet Angular avec routage, nommé **mars-lab**, lui ajouter un dépôt 
Git remote contenant des branches correspondant à toutes les étapes du workshop, et démarrer 
le serveur de développement :
```
ng new mars-lab --routing
cd mars-lab
git remote add origin https://github.com/tchatel/mars-lab.git
git pull
ng serve
```

## Etapes du workshop

### étape 01 : page d’accueil
* changer le titre `<h1>`, laisser <router-outlet>
* `ng generate component home-page`
* définir une route par défaut vers ce composant HomePageComponent
* image d’accueil : `http://localhost:3390/images/rovers/rovers.jpg`
* fichier style.css fourni dans **mars-files.zip**

### étape 02 : module et page rovers
* `ng generate module rovers --routing`
* `ng generate component rovers/page-rovers-list`
* importer RoversModule dans AppModule
* ajouter une route dans rovers-routing
* vérifier en tapant à la main l’url : `http://localhost:4200/rovers` (doit afficher _"page-rovers-list works!"_)

### étape 03 : service et JSON des rovers 
* créer un répertoire core/model dans app
* copier core/model/model.ts (fourni) : interfaces Rover et autres
* `ng generate module core/services`
* `ng generate service core/services/rovers`
* mettre le provider du service dans ServicesModule
* importer les modules ServicesModule et HttpClientModule dans AppModule
* écrire une méthode list() dans RoversService pour charger la liste des rovers
* afficher le JSON

### étape 04 : affichage de la liste des rovers 
* `ng generate component rovers/rover-item`
* ajouter un _input_ au composant : `@Input() rover: Rover;`
* afficher ce composant en bouclant sur la liste des rovers
* copier les fichiers HTML et CSS pour RoverItemComponent
* remplacer les données en dur par des bindings dans son template HTML

### étape 05 : détails des rovers - a) sélection d’un rover
* `ng generate component rovers/rover-details`
* ajouter un _output_ au composant RoverItemComponent
* dans PageRoversList, mettre une propriété contenant le rover sélectionné
* mettre un _input_ dans RoverDetailsComponent
* afficher juste le JSON du rover sélectionné

### étape 06 : détails des rovers - b) données texte et HTML
* RoverDetailsComponent : HTML et CSS fournis
* mettre des bindings dans le template pour la date d’atterrissage et de lancement, le statut, et max sol
* afficher la liste des caméras (séparées par une virgule)
* afficher la description (contenant du HTML)

### étape 07 : détails des rovers - b) formatage des dates et statuts avec des pipes
* pipe “status” pour le statut (en service / HS) :
* `ng generate module core/widgets`
* `ng generate pipe core/widgets/status --export` 
* et importer le module widgets dans RoversModule
* pipe “dateIso” pour les dates :
* `ng generate pipe core/widgets/date-iso --export` 

### étape 08 : animation
* importer BrowserAnimationsModule dans AppModule
* `[@details]="selectedRover.name"`
* CSS: `rover-details {display: block;}`
* fichier page-rovers-list.ts fourni

### étape 09 : module et page photos
* `ng generate module photos --routing`
* `ng generate component photos/page-photos-list`
* importer PhotosModule dans AppModule
* définir la route ‘/photos’
* créer un menu pour naviguer :
* `ng generate component core/widgets/menu --export` 
* importer WidgetsModule dans AppModule
* importer RouterModule dans WidgetsModule
* mettre la balise `<app-menu>` à la place du titre
* CSS fourni pour MenuComponent
* HTML : `<div><nav><a></a><a></a></nav><h1></h1></div>`

### étape 10 : recherche de photos
* `ng generate service core/services/photos`
* mettre le provider du service dans ServicesModule
* méthode search avec rover dans l’URL + query string (caméra & sol)
* affichage du résultat en JSON pour des paramètres en dur (Spirit, NAVCAM, 100)

### étape 11 : affichage des photos
* `ng generate component photos/photo-item`
* afficher : nombre de photos + items répétés
* HTML + CSS fournis

### étape 12 : formulaire de recherche des photos
* `ng generate component photos/photo-search-form`
* formulaire : `<form>Rover<select></select> - Caméra<select></select> - sol<input type=number></form>`
* `[(ngModel)]` + `name`, `[ngValue]` 
* `@Output() search`, avec un type SearchEvent à définir
* validation : rover required, désactiver le bouton si invalide

### étape 13 : photo en grand
* `ng generate component photos/page-big-photo`
* ajouter la route dans PhotosRoutingModule
* le lien sur la vignette devient `<a [routerLink]="['/photos', photo.rover.name, photo.id]">`
* méthode `get(rover: string, photoId: number)` dans PhotosService, url = `http://localhost:3390/rovers/${rover}/photos/${photoId}`
* dans PageBigPhotoComponent, injecter ActivatedRoute, et charger la bonne photo en 
fonction des paramètres de l'URL 

### branche 'nasa' : 
C'est une version du projet n'utilisant pas l'API locale, mais la vraie API de la NASA, si vous voulez 
consulter toutes les photos prises par les 3 rovers.




