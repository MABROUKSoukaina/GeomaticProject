# Documentation de l'Application

Cette application, développée avec l'API d'ArcGIS et Vue.js, permet aux utilisateurs de créer des géométries et d'effectuer des requêtes et des filtres spatiaux.
L'API d'ArcGIS est un ensemble d'outils et de bibliothèques fournissant des fonctionnalités pour développer des applications géospatiales. Elle permet d'intégrer des cartes, des données géographiques et des services de géolocalisation dans des applications web et mobiles, facilitant ainsi la création, la visualisation, l'analyse et la gestion des informations spatiales.

## Configuration Initiale

La première étape pour utiliser l'API consiste à créer un compte sur la plateforme [Esri Developer](https://developers.arcgis.com/). Cela vous permettra d'importer et de gérer des couches dans différents formats, tels que :

- **SHP** (Shapefile)
- **GeoJSON**
- **CSV**

## Création de Géométries

Pour permettre aux utilisateurs de créer des géométries, j'ai mis en place des couches pour les **points**, **polygones** et **lignes**, que j'ai importées sur mon compte Esri
Après avoir importé les couches et activé les modifications nécessaires, nous créons une clé API en lui attribuant les droits d'accès aux fonctionnalités ainsi qu'aux couches souhaitées. Grâce à cette clé, nous pouvons accéder à une multitude de services proposés par la plateforme.

## Fonctionnalités de l'Application

J'ai utilisé les widgets proposés par l'API pour :

- **Créer la carte** : _Map, MapView_
- **Ajouter des couches** : _featureLayer_
- **Afficher la légende** : _ListLayer_
- **Dessiner des graphiques** : _Sketch, Buffer_
- **Modifier le style de chaque couche** : _Renderer, SimpleFillSymbol, SimpleLineSymbol, SimpleMarkerSymbol_
- **Éditer les couches** : _Editor_
- **Tableau de bord** : _ChartMediaInfoValue, MediaContent, PieChartMediaInfo_

## Filtrage des Données

Pour effectuer des filtres sur la couches des stations de transport, je me suis basé sur le **widget Query**, qui permet d'exécuter des requêtes et d'afficher les résultats sur la carte. Étant donné l'absence de clés étrangères entre les couches (région, province, station de transport), j'ai utilisé des requêtes spatiales pour appliquer les filtres nécessaires.

Les filtres utilisés incluent : par région, par province et par type. L'utilisateur peut choisir la valeur désirée, et le filtre s'applique en effectuant un zoom sur la zone concernée. De plus, les utilisateurs peuvent filtrer en dessinant soit une zone de buffer à partir d'une localisation, soit en traçant une géométrie sur la carte.
