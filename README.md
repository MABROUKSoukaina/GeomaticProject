# projet_geomatic
Cette application, développée avec l'API d'ArcGIS et Vue.js, permet aux utilisateurs de créer des géométries et d'effectuer des requêtes et des filtres spatiaux.

*Configuration initiale
La première étape pour utiliser l'API consiste à créer un compte sur la plateforme Esri Developer. Cela permet d'importer et de gérer des couches dans différents formats (par exemple, SHP, GeoJSON) et d'importer des couches à partir de fichiers CSV.

*Création de géométries
Pour permettre aux utilisateurs de créer des géométries, j'ai mis en place des couches pour les points, polygones et lignes, que j'ai importées sur mon compte 

*Fonctionnalités de l'application
J'ai utilisé les widgets proposés par l'API pour :

Créer la carte
Ajouter des couches
Afficher la légende
Dessiner des graphiques
Modifier le style de chaque couche

*Filtrage des données
Pour effectuer des filtres, je me suis basé sur le widget Query, qui permet d'exécuter des requêtes et d'afficher les résultats sur la carte. Étant donné l'absence de clés étrangères entre les couches (région, province, station de transport), j'ai utilisé des requêtes spatiales pour appliquer les filtres nécessaires.




