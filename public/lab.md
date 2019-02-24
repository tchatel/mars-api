# Devoxx France : Angular
## Hands-on Lab : initiation à Angular par la pratique


## Outils nécessaires

* IDE : [WebStorm](https://www.jetbrains.com/webstorm/download/#section=mac) ou autre IDE de JetBrains (démo 30 jours, 
  **recommandé pour les débutants**) ou [Visual Studio Code](https://code.visualstudio.com/download) (gratuit)
* [NodeJS](https://nodejs.org/en/), version 10 minimum
* [Git](https://git-scm.com/downloads)
* extension [Augury](https://augury.rangle.io/) pour Chrome et Firefox


## Installation du workshop

* Angular-CLI **version 7.3.1** (après avoir désinstallé une éventuelle 
version antérieure) :
```
npm uninstall -g @angular/cli
npm install -g @angular/cli@7.3.1 
```

* Cloner le projet mars-api, pour démarrer le serveur d'API (et le laisser tourner) :
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
ng new mars-lab --routing --style=css
cd mars-lab
git remote add origin https://github.com/tchatel/mars-lab.git
git pull
ng serve
```


## Etapes du workshop

### étape 01 : page d’accueil
* dans app.component.html, laisser seulement la balise `<h1>` avec un titre en dur, et la balise `<router-outlet>`
* `ng generate component home-page`
* définir dans app-routing.module.ts une route `'home'` affichant ce composant HomePageComponent
* ajouter une redirection vers cette route `'home'` quand l’URL a un path vide
* afficher sur la page d’accueil l’image : `http://localhost:3390/images/rovers/rovers.jpg`
* fichier _style.css_ fourni dans _mars-files.zip_, à mettre dans le répertoire _src_

### étape 02 : module et page ‘rover’
* `ng generate module rover --routing`
* `ng generate component rover/rover-page`
* importer RoverModule dans AppModule, en laissant le AppRoutingModule à la fin des imports
* ajouter une route `'rover'` dans rover-routing.module.ts, affichant le composant RoverPageComponent
* vérifier en tapant à la main l’url : `http://localhost:4200/rover` (doit afficher _"rover-page works!"_)

### étape 03 : header / menu 
* `ng generate module core/layout`
* `ng generate component core/layout/header --export`
* importer LayoutModule dans AppModule, en laissant le AppRoutingModule à la fin des imports
* dans _app.component.html_, remplacer la balise `<h1>...</h1>` par `<app-header></app-header>`
* écraser les fichiers _.html_ et _.css_ du HeaderComponent par ceux fournis dans _mars-files.zip_
* importer RouterModule dans LayoutModule
* mettre les URL des liens : `[routerLink]="['/home']"` et `/rover` et `/photo` _(la 3ème route n’existant pas encore)_
* ajouter à chaque lien une directive : `routerLinkActive="active"`

### étape 04 : header / menu 
* `ng generate component core/layout/page-not-found`
* afficher un message d’erreur dans le template du PageNotFoundComponent
* définir dans app-routing.module.ts une route pour un path `'**'` affichant ce composant PageNotFoundComponent

### étape 05 : composant pour afficher la liste des rovers 
* `ng generate component rover/rover-list`
* mettre une balise `<app-rover-list>` dans RoverPageComponent, après un titre `<h2>`


### étape 06 : service pour charger la liste des rovers 
* `ng generate service core/services/rover-api`
* injecter ce service au composant RoverListComponent, en rajoutant un argument `(roverApi: RoverApiService)` 
  à son constructeur
* injecter le service standard HttpClient au service RoverApiService, en rajoutant un argument `(http: HttpClient)` 
  à son constructeur
* importer le module HttpClientModule dans AppModule, pour corriger l’erreur “No provider for HttpClient!”


### étape 07 : méthode du service chargeant la liste des rovers
* créer un répertoire _core/model_ dans le répertoire _app_ et y copier _core/model/model.ts_ 
  (fourni dans le fichier zip) : interfaces Rover, Camera et Photo 
* écrire une méthode `list()` dans RoverApiService pour charger la liste des rovers, en faisant une requête GET à 
  l’URL `http://localhost:3390/rovers`

### étape 08 : affichage du JSON reçu
* dans RoverListComponent, déclarer une propriété `roverList$` de type `Observable<Rover[]>`, et dans la méthode 
  `ngOnInit()`, lui affecter l’observable renvoyé par la méthode `list()` du service
* dans le template du composant, afficher dans une balise `<pre>` les données reçues sérialisées en JSON, en utilisant 
  les pipes _async_ et _json_ : <br>
  `<pre>{{roverList$ | async | json}}</pre>` 
* vérifier que le JSON s’affiche bien

### étape 09 : affichage des noms des rovers
* dans le template du RoverListComponent, répéter un élément `<div>` avec :<br>
  `*ngFor="let rover of roverList$ | async"`
* dans le `<div>` répété, afficher le nom du rover

### étape 10 : affichage de la liste des rovers
* `ng generate component rovers/rover-item`
* écraser les fichiers HTML et CSS du RoverItemComponent par ceux fournis
* afficher ce composant répété pour chacun des rovers, dans le template du RoverListComponent, avec une 
  balise `<app-rover-item>` et la même directive `*ngFor` que précédemment
  
* ajouter un _input_ au RoverItemComponent :<br>
  `@Input() rover: Rover;`
* alimenter l’_input_ du RoverItemComponent en ajoutant un attribut `[rover]="rover"` à la balise `<app-rover-item>`
* remplacer les données en dur par des bindings dans le template du RoverItemComponent

### étape 11 : griser les images des rovers qui ne sont plus actifs
* dans RoverItemComponent, ajouter une méthode `isRoverDead()` renvoyant _true_ si `rover.status` est différent de `'active'`
* dans le template, mettre une classe CSS conditionnelle sur le premier `<div>` : `[class.dead]=”isRoverDead()”`

### étape 12 : sélection d’un rover de la liste
* ajouter un output au composant RoverItemComponent :<br>
  `@Output() select = new EventEmitter<Rover>();`
* dans son template, intercepter le click sur le premier `<div>`, et émettre cet événement _select_, avec l’objet rover 
  comme valeur d’événement : `(click)="select.emit(rover)"`
* dans son fichier CSS, mettre `cursor: pointer;` pour l’item complet
* dans RoverListComponent, mettre une propriété `selectedRover` qui contiendra le rover sélectionné
* dans son template, intercepter l’événement _select_ du composant enfant, et affecter la valeur de l’événement 
  à la propriété _selectedRover_ : <br>
  `(select)="selectedRover = rover"`
* afficher le JSON du rover sélectionné sous les rovers répétés

### étape 13 : mettre en évidence le rover sélectionné
* ajouter un input `selected`, de type booléen, au composant RoverItemComponent
* alimenter cet input dans le template du composant parent :<br>
  `[selected]="selectedRover === rover"`
* mettre une classe CSS conditionnelle _selected_ sur le premier `<div>` du RoverItemComponent, pour que le rover 
  sélectionné soit mis en évidence dans la liste


### étape 14 : détails du rover sélectionné - données simples
* `ng generate component rover/rover-details`
* écraser les fichiers HTML et CSS du RoverDetailsComponent avec ceux fournis
* afficher ce composant dans le template de RoverListComponent, à la place du JSON de l’étape précédente
* mettre un _input_ dans RoverDetailsComponent, pour lui passer le rover concerné
* mettre des bindings dans le template pour les propriétés suivantes du rover : `name`, `launch_date`, `landing_date`, 
  et `max_sol` (nombre de jours solaires martiens en activité)

### étape 15 : détails du rover sélectionné - liste des caméras (1ère version)
* afficher la liste des caméras (séparées par une virgule), en utilisant un élément répété avec `*ngFor`
* la syntaxe de `*ngFor` permet de récupérer un booléen `last`, à utiliser avec `*ngIf` pour ne pas ajouter une virgule 
  après la dernière caméra de la liste :<br>
  `*ngFor="let camera of rover.cameras ; last as isLast"`
  
### étape 16 : détails du rover sélectionné - liste des caméras (2ème version)
* afficher à nouveau la liste des caméras séparées par une virgule, construite cette fois par une méthode du composant 
  qui renvoie la chaîne de caractères complète
  
### étape 17 : détails du rover sélectionné - description en HTML
* alimenter la propriété _innerHTML_ du `<div>` avec la description du rover, qui contient du HTML

### étape 18 : détails des rovers - formatage avec des pipes standards
* utiliser le pipe standard `uppercase` pour afficher le nom du rover en majuscules
* utiliser le pipe standard `date` pour formater la date
* lui mettre un argument pour indiquer le format de date souhaité (26/11/2011)

### étape 19 : détails du rover sélectionné - affichage du statut avec un pipe spécifique
* `ng generate module shared`
* `ng generate pipe shared/status --export`
* importer SharedModule dans RoverModule (en laissant le routing module à la fin)
* écrire le code de la méthode du pipe, pour renvoyer _'en service'_ si status vaut _'active'_, ou _'hors service'_
  dans le cas contraire

### étape 20 : déplacer le RoverDetailsComponent dans RoverPageComponent
_Il n’est pas logique que le composant affichant les détails fasse partie du composant liste, on va le déplacer 
  dans le composant parent RoverPageComponent._
* déplacer la balise `<app-rover-detail>` complète de _rover-list.component.html_ vers _rover-page.component.html_
* ajouter la propriété `selectedRover` dans RoverPageComponent (sans enlever celle de RoverListComponent), 
  et l’alimenter, quand le composant PageRoverList émet un événement `select`, avec la valeur de l’événement
* ajouter cet output `select` dans RoverListComponent
* toujours dans RoverListComponent, ajouter une méthode `selectComponent` prenant un rover en paramètre, qui met ce 
  rover dans la propriété `selectedRover` et l’émet comme valeur de l’événement `select`
* dans son template _rover-list.component.html_, appeler cette méthode `selectRover` quand le composant enfant 
  RoverItemComponent émet son événement `select`

### étape 21 : module et page photo
* `ng generate module photo --routing`
* `ng generate component photo/photo-page`
* importer PhotoModule dans AppModule
* définir dans _photos-routing.module.ts_ la route `'photo'` affichant le PhotoPageComponent
* vérifier que ça afffiche _“photo-page works!”_ quand on clique sur le lien _“Photos”_ du menu

### étape 22 : formulaire de recherche des photos
* `ng generate component photo/photo-search-form`
* écraser les fichiers HTML et CSS du composant par ceux fournis
* afficher le formulaire, en mettant une balise `<app-photo-search-form>` dans _photo-page.component.html_
* définir dans PhotoSearchFormComponent, en dehors de la classe, une interface exportée : <br>
  `export interface SearchData { rover?: Rover; camera?: Camera; sol?: number; }`
* définir dans PhotoSearchFormComponent une propriété `formData` de type `SearchData` et initialisée avec un objet vide
* afficher provisoirement cette propriété `formData`, sérialisée en JSON, en dessous du formulaire

### étape 23 : formulaire : champ numérique Sol
* lier le champ _Sol_ du formulaire à la propriété `formData.sol`, grâce à la directive _ngModel_ :
  `[(ngModel)]="formData.sol"`
* importer FormsModule dans PhotoModule (en laissant le routing module à la fin)
* ajouter au champ _Sol_ un attribut `name`, qui est obligatoire quand on utilise la directive ngModel à l’intérieur 
  d’une balise <form>

### étape 24 : formulaire : liste déroulante des rovers
* mettre de la même façon sur le `<select>` du rover les attributs `name="rover"`  et `[(ngModel)]="formData.rover"`
* récupérer la liste des rovers fournie par l’API, sous la forme d’un observable, en appelant la méthode `list()` du 
  RoverApiService, comme dans RoverListComponent
* répéter l’option non vide du `<select>` pour chacun des rovers de la liste, en utilisant le pipe `async`
* pour sélectionner l’objet rover complet, ajouter à l’option : `[ngValue]="rover"`
* ajouter aussi `[ngValue]="undefined"` à l’option vide

### étape 25 : formulaire : liste déroulante des caméras
* proposer dans la liste déroulante des caméras les noms des caméras du rover sélectionné
* pour ne pas avoir une erreur quand il n’y a pas de rover sélectionné, on peut utiliser l’opérateur Elvis : `?.` : <br>
  `*ngFor="let camera of formData.rover?.cameras"`
* ajouter les attributs `[(ngModel)]` et `name` sur la balise `<select>`, et l’attribut `[ngValue]` sur les 
  balises `<option>`, pour récupérer l’objet caméra complet dans la propriété `formData` 
* il faut annuler la caméra sélectionnée lors du changement de rover, grâce à un attribut sur le `<select>` du rover: <br>
  `(ngModelChange)="formData.camera = undefined"`

### étape 26 : validation du formulaire
_Pour la recherche, le nom du rover sera obligatoire, car l'API en a besoin._
* rendre le `<select>` du rover obligatoire, avec l’attribut HTML standard `required`
* ajouter à la balise `<form>` un attribut `#formInfo="ngForm"`, pour récupérer dans une variable de template 
  nommée _formInfo_ l’instance de la directive _NgForm_ qui a été automatiquement appliquée par Angular à la balise `<form>`
* ajouter au bouton de soumission du formulaire un attribut `[disabled]="formInfo.invalid"` pour le désactiver quand 
  le formulaire est invalide

### étape 27 : message d’erreur
_On va afficher un message demandant de sélectionner un rover, si le <select> correspondant est en erreur._
* ajouter à la balise `<select>` un attribut `#roverInfo="ngModel"` pour récupérer dans une variable de template 
  nommée _roverInfo_ l’instance de la directive _NgModel_ appliquée à cet élément HTML
* ajouter après le bouton un message, affiché seulement si le champ a une erreur _'required'_ :<br>
  `<span *ngIf="roverInfo.hasError('required')">Veuillez choisir un rover.</span>`

### étape 28 : soumission du formulaire
_Quand le formulaire est soumis, ce n’est pas le composant PhotoSearchFormComponent qui va faire la recherche, mais 
 son composant parent PhotoPageComponent._
* ajouter un _output_ au composant PhotoSearchFormComponent, pour émettre un événement _search_ : <br>
  `@Output() search = new EventEmitter<SearchData>();`
* dans la balise `<form>` du template, ajouter un attribut interceptant l’événement Angular `ngSubmit` (et non pas 
  l’événement standard HTML _submit_) et émettant l’événement _search_ : <br>
  `(ngSubmit)="search.emit(formData)"`
* enlever le JSON qui était provisoirement affiché en dessous du formulaire
* dans le composant parent PhotoPageComponent, intercepter cet événement _search_, et appeler une méthode _doSearch_ 
 (à créer) en lui passant l’événement en paramètre, et qui pour l’instant se contente de faire un `console.log()` de
 la valeur reçue
 
### étape 29 : recherche
* `ng generate service core/services/photo-api`
* injecter le service `HttpClient` au PhotoApiService
* créer une méthode pour faire la recherche : <br>
  `search(roverName: string, cameraName?: string, sol?: number): Observable<Photo[]>`<br>
  l’URL doit être du type : `http://localhost:3390/rovers/Curiosity/photos?camera=NAVCAM&sol=100` avec les paramètres 
  dans cet ordre, sans paramètre ayant une valeur vide, et sans `?` s’il n’y a aucun paramètre
* dans PhotoPageComponent, injecter le nouveau service
* et à la place du `console.log()` de la méthode `doSearch()`, appeler la méthode `search()` du nouveau service, 
  mettre l’observable reçu dans une propriété `photoList$`, et afficher le JSON dans le template, en utilisant les 
  pipes `async` et `json`

### étape 30 : affichage des photos
* `ng generate component photo/photo-list`
* `ng generate component photo/photo-item`
* écraser les fichiers _.html_ et _.css_ du PhotoItemComponent par ceux fournis 
* mettre un _input_ `@Input() photoList: Photo[];` dans PhotoListComponent, et insérer ce composant avec son input 
  alimenté à la place de l’affichage du JSON dans le template de PhotoPageComponent
* dans le template de PhotoListComponent, répéter un PhotoItemComponent pour chacune des photos trouvées
* mettre un _input_ `@Input() photo: Photo;` dans PhotoItemComponent, et l’alimenter dans le composant liste
* mettre des bindings dans le template de PhotoItemComponent pour les photos et leur légende
* afficher aussi le nombre de résultats, dans PhotoListComponent, avant la série de photo




