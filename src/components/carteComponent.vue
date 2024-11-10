<template>
  <div>
    <div id="viewDiv">
    </div>
  </div>
</template>
<script>
import controlComponent from './controlComponent.vue';
import BufferComponent from './bufferComponent.vue';
import dashComponent from './dashComponent.vue';
import { Chart, DoughnutController, ArcElement, Tooltip, Legend, Title } from 'chart.js';
import esriConfig from "@arcgis/core/config.js";
import Map from '@arcgis/core/Map.js';
import MapView from '@arcgis/core/views/MapView.js';
import FeatureLayer from "@arcgis/core/layers/FeatureLayer.js";
import Search from '@arcgis/core/widgets/Search.js'
import Expand from "@arcgis/core/widgets/Expand.js";
import Editor from "@arcgis/core/widgets/Editor.js";
import Directions from "@arcgis/core/widgets/Directions.js";
import ScaleBar from "@arcgis/core/widgets/ScaleBar.js";
import RouteLayer from "@arcgis/core/layers/RouteLayer.js";
import PopupTemplate from '@arcgis/core/PopupTemplate.js';
import SimpleMarkerSymbol from "@arcgis/core/symbols/SimpleMarkerSymbol.js";
import SimpleRenderer from "@arcgis/core/renderers/SimpleRenderer";
import SimpleLineSymbol from "@arcgis/core/symbols/SimpleLineSymbol";
import SimpleFillSymbol from "@arcgis/core/symbols/SimpleFillSymbol"; 
import BasemapGallery from "@arcgis/core/widgets/BasemapGallery.js";
import LayerList from "@arcgis/core/widgets/LayerList.js";
import Query from "@arcgis/core/rest/support/Query.js";
import * as geometryEngine from "@arcgis/core/geometry/geometryEngine.js";
import Sketch from "@arcgis/core/widgets/Sketch.js";
import GraphicsLayer from "@arcgis/core/layers/GraphicsLayer.js";
import SketchViewModel from "@arcgis/core/widgets/Sketch/SketchViewModel.js";
import Slider from "@arcgis/core/widgets/Slider.js";
import Graphic from "@arcgis/core/Graphic.js";

export default {
  name: 'App',
  components: {
    controlComponent,
    BufferComponent,
    dashComponent,
  },
  mounted() {
    esriConfig.apiKey = "AAPTxy8BH1VEsoebNVZXo8HurGH-dZoa20n9-eNCQSaz_b8Vf2ywp63QOLvqTbIg3nayMlmtcs-MivrHz2OoWOxL5zmYk_jG1Ka3c_d1_6Gd2etxIhjaEPMFL3kicNL52B6G2U3FxpyitUI6Bzm2hYQE3b2aTvEN2VI1wZ3BaAkFEr3hlSJuf0zcHCgOoFjTdqB3MgdugKfmRRJYD0QPIIPGsm48ddHYouQi9GkzzM55_V1qHhMpJG_849ULhFPMh7cLSgaQrIo1U-uYdrB0TGXHNQ..AT1_nZs5gMpl";
    const itinéraires = new RouteLayer();
    const graphicsLayer = new GraphicsLayer({ title: "graphicsLayer" });
    const map = new Map({
      layers: [itinéraires, graphicsLayer],
      basemap:"topo-vector"
    });

    const view = new MapView({
      map: map,
      center: [-7.092620, 31.791702],
      zoom: 6,
      container: "viewDiv"
    });

    //Renderer pour la symbologie
    const ProvinceRenderer = new SimpleRenderer({
      symbol: new SimpleFillSymbol({
        color: [255, 165, 0, 0],
        outline: {
          color: "black",
          width: 1
        }
      })
    });

    const RegionRenderer = new SimpleRenderer({
      symbol: new SimpleFillSymbol({
        color: [255, 165, 0, 0],
        outline: {
          color: [210, 105, 30],
          width: 1
        }
      })
    });

    const SelectedRegionRenderer = new SimpleRenderer({
      symbol: new SimpleFillSymbol({
        color: [255, 165, 0, 0.3],  // Augmente la transparence (alpha 0.3 pour un léger effet de sélection)
        outline: {
          color: [0, 0, 255],       // Utilise une couleur plus vive pour le contour (ex. : bleu)
          width: 2
        }
      })
    });


    var Stationsrenderer = new SimpleRenderer({
      symbol: new SimpleMarkerSymbol({
        color: "blue",
        size: 4
      })
    });

    var popupProvince = new PopupTemplate({
      title: "Province {Nom}",


    });

    const provinces = new FeatureLayer({
      url: "https://services3.arcgis.com/09qwKWHRBuUoUCW6/arcgis/rest/services/provinces/FeatureServer/0",
      outFields: ["Nom"],
      popupTemplate: popupProvince,
      renderer: ProvinceRenderer,
    });

    map.add(provinces);

    const popupregion = {
      title: "la région {NOM_REG}",
    };
    const regions = new FeatureLayer({
      url: "https://services3.arcgis.com/09qwKWHRBuUoUCW6/arcgis/rest/services/regions/FeatureServer/0",
      outFields: ["NOM_REG"],
      popupTemplate: popupregion,
      renderer: RegionRenderer
    });
    map.add(regions);

    const popupstation = {
      title: "les nformations sur la station",
      content: "<p><span style='font-weight: bold; color: #222;'>Nom de la station</span> : {name}</p>" +
        "<p><span style='font-weight: bold; color: #222;'>Type de la station</span> : {fclass}</p>"

    };

    const stations_transport = new FeatureLayer({
      url: "https://services3.arcgis.com/09qwKWHRBuUoUCW6/arcgis/rest/services/couche_transport/FeatureServer/0",
      outFields: ["name", "fclass", "osm_id", "id"],
      popupTemplate: popupstation,
      renderer: Stationsrenderer
    });
    map.add(stations_transport);

    var Polygonerenderer = new SimpleRenderer({
      symbol: new SimpleFillSymbol({
        color: "purple", // Couleur bleue avec transparence de 30%
      })
    });

    var Lignesrenderer = new SimpleRenderer({
      symbol: new SimpleLineSymbol({
        color: "red", // Couleur bleue avec transparence de 30%
        width: 2
      })
    });
    var pointSymbol = {
      type: "simple-marker",
      color: "darkgreen",
      size: 10,
      outline: {
        color: "lightgreen",
        width: 3
      }
    };


    var Pointrenderer = new SimpleRenderer({
      symbol: pointSymbol
    })


    var popupPolygones = new PopupTemplate({
      title: "Informations sur le polygone crée",
      content: `
    <div style='font-size: 1em; color: #555;'>
      <p><span style='font-weight: bold; color: #222;'>Code</span> : {FID}</p>
      <p><span style='font-weight: bold; color: #222;'>Nom</span> : {Nom}</p>
      <p><span style='font-weight: bold; color: #222;'>Description</span> : {Descrp}</p>
    </div>
  `
    });

    //Ajout de la couche des polygones
    const polygones = new FeatureLayer({
      url: "https://services3.arcgis.com/09qwKWHRBuUoUCW6/arcgis/rest/services/Polygones/FeatureServer/0",
      outFields: ["FID", "Nom", "Descrp"],
      popupTemplate: popupPolygones,
      renderer: Polygonerenderer
    });
   map.add(polygones);

    var popupLignes = new PopupTemplate({
      title: "Informations sur la ligne crée",
      content: "<p><span style='font-weight: bold;'>Code</span> : {FID} </p>" +
        "<p> <span style='font-weight: bold;'>Nom</span> : {Nom} </p>" +
        "<p><span style='font-weight: bold;'>Déscription</span> : {Descrip} </p>"
    });

    // ajout de la couche des lignes
    const lignes = new FeatureLayer({
      url: "https://services3.arcgis.com/09qwKWHRBuUoUCW6/arcgis/rest/services/Lignes/FeatureServer/0",
      outFields: ["FID", "Nom", "Descrip"],
      popupTemplate: popupLignes,
      renderer: Lignesrenderer
    });
    map.add(lignes);

    var popupPoints = new PopupTemplate({// Popup affichant les information sur les points crées
      title: "Informations sur le point crée",
      content: "<p><span style='font-weight: bold;'>Code</span> : {FID} </p>" + "<p> <span style='font-weight: bold;'>Nom</span> : {Nom} </p>" + "<p><span style='font-weight: bold;'>Description</span> : {Descpt} </p>",

    });

    // ajout de la couche des points 
    const points = new FeatureLayer({
      url: "https://services3.arcgis.com/09qwKWHRBuUoUCW6/arcgis/rest/services/Points/FeatureServer/0",
      outFields: ["FID", "Nom", "Descpt"],
      popupTemplate: popupPoints,
      renderer: Pointrenderer

    });
    map.add(points)

    var selectedpointSymbol = {
      type: "simple-marker",
      color: "darkblue",
      size: 10,
      outline: {
        color: "lightblue",
        width: 3
      }
    };
    var selectedtypeSymbol = {
      type: "simple-marker",
      color: "red",
      size: 10,
      outline: {
        color: "pink",
        width: 3
      }
    };


    var selectedPointrenderer = new SimpleRenderer({
      symbol: selectedpointSymbol
    })

    var selectedTyperenderer = new SimpleRenderer({
      symbol: selectedtypeSymbol
    })

    // Créer l'instance de LayerList
    const layerList = new LayerList({
      view: view,
      listItemCreatedFunction: (event) => {
        const item = event.item;
        if (item.layer.type !== "group") {
          // Éviter d'afficher la légende deux fois
          item.panel = {
            content: "legend",
            open: true
          };
        }
      }
    });

    const coordinatesWidget = document.createElement("div");
    coordinatesWidget.id = "coordinatesWidget";
    // Ajoutez le widget de coordonnées à la vue
    view.ui.add(coordinatesWidget, "bottom-left");
    // Créer l'instance de Expand
    const layerListExpand = new Expand({
      view: view,
      content: layerList,
      expanded: false // Détermine si l'Expand est ouvert par défaut
    });

    view.ui.add(layerListExpand, "bottom-right");

    view.ui.add(
      new Expand({
        view: view,
        content: new BasemapGallery({
          view: view,
          style: "card"
        }),
        expanded: false,
      }),
      "bottom-right"
    );

    const scaleBar = new ScaleBar({
      view: view
    });
    view.ui.add(scaleBar, {
      position: "bottom-left"
    });

    view.ui.add(
      new Expand({
        view: view,
        content: new Directions({
          view: view,
          layer: itinéraires
        })
      }),
      "top-left"
    );

    view.ui.add(
      new Expand({
        view: view,
        content: new Search({
          view: view
        })
      }),
      "top-left"
    );


    // fonction pour zommer sur la couche 

    function zoomToLayer(layer) {
      return layer.queryExtent().then((response) => {
        view.goTo(response.extent)
          .catch((error) => {
            console.error(error);
          });
      });
    }
    // recuperer les selecteurs 
    const selectRegion = document.getElementById("region");
    const selectProvince = document.getElementById("province");
    const selectType = document.getElementById("stationtype");

    const addedRegions = new Set();
    const addedProvinces = new Set();
    const addedType = new Set();

    const REGION = {
      where: '1=1',
      outFields: ["NOM_REG"], // Attributes to return
      returnGeometry: true
    }

    const Province = {
      where: '1=1',
      outFields: ["Nom"], // Attributes to return
      returnGeometry: true
    }

    const station_type = {
      where: '1=1',
      outFields: ["fclass"], // Attributes to return
      returnGeometry: true
    }

    // charger le selecteur par les noms des regions à partir de la couche region
    regions.queryFeatures(REGION).then((results) => {
      const features = results.features;

      for (const feature of features) {
        const region = feature.attributes["NOM_REG"];
        if (!addedRegions.has(region)) {
          let option = document.createElement("option");
          option.value = region;
          option.textContent = region;
          selectRegion.appendChild(option);
          addedRegions.add(region);
        }
      }
    })

    // // charger le selecteur par les noms des provinces à partir de la couche province
    provinces.queryFeatures(Province).then((results) => {
      const features = results.features;

      for (const feature of features) {
        const province = feature.attributes["Nom"];
        if (!addedProvinces.has(province)) {
          let option = document.createElement("option");
          option.value = province;
          option.textContent = province;
          selectProvince.appendChild(option);
          addedProvinces.add(province);
        }
      }
    })
    // // charger le selecteur par les types de station à partir de la couche transport_station
    stations_transport.queryFeatures(station_type).then((results) => {
      const features = results.features;
      for (const feature of features) {
        const station = feature.attributes["fclass"];
        if (station && station.trim() !== "" && !addedType.has(station)) {
          let option = document.createElement("option");
          option.value = station;
          option.textContent = station;
          selectType.appendChild(option);
          addedType.add(station);
        }
      }
    })

    // Fonction principale pour gérer le filtrage par région et province
    async function handleRegionChange(selectedRegion) {
      try {
        if (!selectedRegion) return;

        //  Définir le filtre pour la région et mettre à jour le rendu
        const filterValue = `NOM_REG='${selectedRegion}'`;
        regions.definitionExpression = filterValue;
        regions.renderer = SelectedRegionRenderer;
        await zoomToLayer(regions);

        //  Récupérer la géométrie de la région sélectionnée
        const regionGeometry = await getRegionGeometry(selectedRegion);

        //  Filtrer les stations à l'intérieur de la région
        FilterStationsInGeometry(regionGeometry);

        // Récupérer les provinces associées à la région
        await getProvinces(regionGeometry);

      } catch (err) {
        console.error("Erreur lors du traitement de la région sélectionnée : ", err);
      }
    }

    async function handleProvinceChange(selectedProvince) {
      try {
        if (!selectedProvince) return;

        //  Définir le filtre pour la province et mettre à jour le rendu
        const filterValue = `Nom='${selectedProvince}'`;
        provinces.definitionExpression = filterValue;
        provinces.renderer = SelectedRegionRenderer;
        await zoomToLayer(provinces);
        //  Récupérer la géométrie de la province sélectionnée
        const provinceGeometry = await getProvinceGeometry(selectedProvince);
        //  Filtrer les stations à l'intérieur de la province
        FilterStationsInGeometry(provinceGeometry)
      } catch (err) {
        console.error("Erreur lors du traitement de la région sélectionnée : ", err);
      }
    }
  

    async function FilterStationsInGeometry(Geometry) {
  clearSelectionStations();
  const query = stations_transport.createQuery();
  query.geometry = Geometry;
  query.spatialRelationship = "intersects";
  query.outFields = ["id", "fclass"];

  try {
    const results = await stations_transport.queryFeatures(query);
    const filteredStationsCount = results.features.length;
    console.log("Nombre de stations filtrées:", filteredStationsCount);

    if (filteredStationsCount > 0) {
      const stationsIds = results.features.map(feature => feature.attributes.id).join(', ');
      stations_transport.definitionExpression = `id IN (${stationsIds})`;
      stations_transport.renderer = selectedPointrenderer;
      document.getElementById("count").textContent = filteredStationsCount;
      // Mettre à jour la chart uniquement avec les stations filtrées
      updateChartWithFilteredData(results.features);
    } else {
      // Si aucun point n'est trouvé, réinitialise le graphique aux données par défaut
      stations_transport.definitionExpression = null;
      initializeChartWithAllStations();
    }
  } catch (error) {
    console.error("Erreur lors de la requête de filtre :", error);
  }
}
    // Fonction pour effacer les surbrillances
    function clearSelectionStations() {
      stations_transport.definitionExpression = ''
      stations_transport.renderer = Stationsrenderer

    }

function updateChartWithFilteredData(features) {
  // Compter le nombre de stations par type (fclass)
  const counts = {
    vide: 0, bus_stop: 0, ferry_terminal: 0, airport: 0,
    bus_station: 0, taxi: 0, railway_station: 0,
    railway_halt: 0, helipad: 0, tram_stop: 0
  };

  // Parcourir les résultats et compter chaque type de station
  features.forEach(feature => {
    const fclass = feature.attributes.fclass || "vide"; // Utiliser "vide" si `fclass` est vide
    if (counts.hasOwnProperty(fclass)) {
      counts[fclass]++;
    }
  });

  // Mettre à jour le graphique avec les nouvelles données
  updateChart(pieChart, [
    counts.vide,
    counts.bus_stop,
    counts.ferry_terminal,
    counts.airport,
    counts.bus_station,
    counts.taxi,
    counts.railway_station,
    counts.railway_halt,
    counts.helipad,
    counts.tram_stop
  ]);
}

    // Fonction pour récupérer la géométrie de la région sélectionnée
    async function getRegionGeometry(regionNom) {
      const query = new Query();
      query.where = `NOM_REG = '${regionNom}'`;
      query.returnGeometry = true;

      const result = await regions.queryFeatures(query);
      if (result.features.length > 0) {
        console.log("La géométrie de la région a été récupérée avec succès.");
        return result.features[0].geometry;
      } else {
        throw new Error("Aucune région trouvée avec ce nom.");
      }
    }
    async function getProvinceGeometry(provinceNom) {
      const query = new Query();
      query.where = `Nom = '${provinceNom}'`;
      query.returnGeometry = true;
      const result = await provinces.queryFeatures(query);
      if (result.features.length > 0) {
        console.log("La géométrie de la province a été récupérée avec succès.");
        return result.features[0].geometry;
      } else {
        throw new Error("Aucune province trouvée avec ce nom.");
      }
    }

    // Fonction pour récupérer les provinces à l'intérieur d'une région
    async function getProvinces(regionGeometry) {
      const query = new Query();
      query.geometry = regionGeometry;
      query.spatialRelationship = "intersects";
      query.returnGeometry = true; // Nous avons besoin de la géométrie pour calculer l'aire
      query.outFields = ["Nom"];

      const result = await provinces.queryFeatures(query);
      selectProvince.innerHTML = ""; // Effacer les anciennes options

      if (result.features.length > 0) {
        console.log("Provinces récupérées avec succès.");

        result.features.forEach(feature => {
          const provinceGeometry = feature.geometry;

          // Calculer l'intersection entre la région et la province
          const intersection = geometryEngine.intersect(regionGeometry, provinceGeometry);

          if (intersection) {
            const intersectionArea = geometryEngine.geodesicArea(intersection, "square-kilometers");
            const provinceArea = geometryEngine.geodesicArea(provinceGeometry, "square-kilometers");

            // Vérifier si l'aire d'intersection est significative
            if (intersectionArea / provinceArea > 0.) { // par exemple, plus de 50% de la province est dans la région
              const option = document.createElement("option");
              option.value = feature.attributes.Nom;
              option.text = feature.attributes.Nom;
              selectProvince.add(option);
            }
          }
        });
      } else {
        console.log("Aucune province trouvée pour cette région.");
      }
    }

    // Événement de changement sur le sélecteur de région
    selectRegion.addEventListener("change", (event) => {
      const selectedRegion = event.target.value;
      handleRegionChange(selectedRegion);
    });
    selectProvince.addEventListener("change", (event) => {
      const selectedProvince = event.target.value;
      handleProvinceChange(selectedProvince);
    });

    async function handleTypeChange(selectedType) {
      try {
        

        // Créer une nouvelle expression de définition
        let newDefinitionExpression = '';

        // Vérifier s'il y a déjà une expression de définition
        if (stations_transport.definitionExpression) {
          // Extraire les filtres existants
          const existingFilters = stations_transport.definitionExpression.split(' AND ');

          // Filtrer pour garder tous les filtres sauf celui par type
          const filteredFilters = existingFilters.filter(filter => !filter.startsWith('fclass'));

          // Reconstituer l'expression avec les filtres restants
          newDefinitionExpression = filteredFilters.join(' AND ');
        }

        // Ajouter le nouveau filtre par type
        newDefinitionExpression += (newDefinitionExpression ? ' AND ' : '') + `fclass = '${selectedType}'`;

        // Mettre à jour l'expression de définition
        stations_transport.definitionExpression = newDefinitionExpression;

        // Appliquer le rendu sur les stations filtrées par type
        stations_transport.renderer = selectedTyperenderer;
        

        console.log("Stations filtrées par type :", selectedType);
        console.log("Nouvelle expression de définition :", stations_transport.definitionExpression);

        const query = stations_transport.createQuery();
       query.where = newDefinitionExpression; // Utilise la nouvelle expression de définition
       query.outFields = ["fclass"]; // Récupère les champs nécessaires pour le graphique

        const result = await stations_transport.queryFeatures(query);
        const filteredStationsCount = result.features.length;
       
       document.getElementById("count").textContent = filteredStationsCount;
    // Mettre à jour la chart avec les données filtrées
    updateChartWithFilteredData(result.features);

      } catch (err) {
        console.error("Erreur lors de l'application du filtre de type : ", err);
      }
    }
    selectType.addEventListener("change", (event) => {
      const selectedType = event.target.value;
      handleTypeChange(selectedType);
    });
    //supprimer tous les filtres
    const InitDiv = document.getElementById("initialiser");
    function Actualiser() {
       stations_transport.renderer = Stationsrenderer
      regions.renderer = RegionRenderer
      provinces.renderer= ProvinceRenderer
      stations_transport.definitionExpression = null;
      regions.definitionExpression = null;
      provinces.definitionExpression = null;
      stations_transport.refresh();
      provinces.refresh();
      regions.refresh();
      stations_transport.refresh();
      zoomToLayer(stations_transport)
    }
    InitDiv.addEventListener('click', function () {
      Actualiser();
    });


    // recherche par une geomertrie dessinée
    

    // Créer une instance de Sketch
    const sketch = new Sketch({
      view: view,
      layer: graphicsLayer,
      creationMode: "update"
    });

    // Ajouter un écouteur d'événement pour récupérer la géométrie de la forme
    // Écouteur d'événement pour gérer la création et l'édition de formes
    sketch.on("create", async (event) => {
      if (event.state === "complete") {
        console.log("Dessin complet !");
        // Récupérer la géométrie de la forme dessinée
        const polygonGeometry = event.graphic.geometry;
        // Appliquer le filtre spatial sur la couche des stations de transport
        await filterStations(polygonGeometry);
      } else {
        console.log("État du dessin :", event.state);
      }
    });

    // Écouteur d'événement pour gérer la suppression de formes
    sketch.on("delete", () => {
      console.log("Forme supprimée, suppression du filtre...");
      // Réinitialiser le filtre sur la couche des stations de transport
      stations_transport.definitionExpression = null; // Supprimer le filtre
      stations_transport.renderer = Stationsrenderer; // Optionnel : réinitialiser le rendu
      console.log("Filtre réinitialisé.");
    });

    // Fonction pour filtrer les stations de transport
    async function filterStations(polygon) {
      console.log("Début du filtrage des stations...");

      try {
        // Créer une requête pour la couche des stations de transport
        const query = stations_transport.createQuery();
        query.geometry = polygon; // Définir la géométrie du filtre
        query.spatialRelationship = "intersects"; // Définir la relation spatiale
        query.returnGeometry = true; // Si vous souhaitez renvoyer la géométrie
        query.outFields = ["id"]; // Récupérer uniquement l'ID

        // Exécuter la requête pour obtenir les résultats
        const result = await stations_transport.queryFeatures(query);

        // Vérifiez si des résultats sont retournés
        if (result.features.length > 0) {
          // Récupérer les ID des stations et les joindre pour l'expression de définition
          const stationsIds = result.features.map(feature => feature.attributes.id).join(', ');
          console.log("Stations trouvées :", stationsIds);

          // Appliquer l'expression de définition pour filtrer la couche
          stations_transport.definitionExpression = `id IN (${stationsIds})`;
          stations_transport.renderer = selectedPointrenderer; // Définir le rendu personnalisé
        } else {
          console.log("Aucune station trouvée dans la zone de dessin.");
          stations_transport.definitionExpression = null; // Réinitialiser si aucun point n'est trouvé
        }
      } catch (error) {
        console.error("Erreur lors de la requête de filtre :", error);
      }
    }


    const sketchExpand = new Expand({
      view: view,
      content: sketch,
      expanded: false,
      expandIcon: "pencil-mark",
      expandTooltip: "Filtrer les stations de transport  par géometrie"

    });
    view.ui.add(sketchExpand, "top-right");

    const editor = new Editor({
      view: view,
    });
    const editorExpand = new Expand({
      view: view,
      content: editor,
      expanded: false,
      expandTooltip: "Editer les couches (points, lignes, polygones)"

    });

    view.ui.add(editorExpand, "top-right");

    // afficher le sketch sur la carte une fois on presse le bouton avec id sketch
    document.getElementById("sketch").addEventListener("click", function () {
      sketchExpand.expanded = true;
    });

    document.getElementById("editer").addEventListener("click", function () {
      editorExpand.expanded = true
    });

    // // rechercher par buffer

    const sketchLayer = new GraphicsLayer();
    const bufferLayer = new GraphicsLayer();
    view.map.addMany([bufferLayer, sketchLayer]);

    let sketchGeometry = null;
    const sketchViewModel = new SketchViewModel({
      layer: sketchLayer,
      defaultUpdateOptions: {
        tool: "reshape",
        toggleToolOnClick: false
      },
      view: view,
      defaultCreateOptions: { hasZ: false }
    });

    sketchViewModel.on("create", (event) => {
      if (event.state === "complete") {
        sketchGeometry = event.graphic.geometry;
        runQuery(); // Appel de la fonction runQuery pour dessiner le buffer et surligner les stations
      }
    });

    sketchViewModel.on("update", (event) => {
      if (event.state === "complete") {
        sketchGeometry = event.graphics[0].geometry;
        runQuery(); // Appel de la fonction runQuery pour mettre à jour le buffer et les stations
      }
    });

    // Draw geometry buttons
    const pointBtn = document.getElementById("point-geometry-button");
    const lineBtn = document.getElementById("line-geometry-button");
    const polygonBtn = document.getElementById("polygon-geometry-button");
    pointBtn.addEventListener("click", geometryButtonsClickHandler);
    lineBtn.addEventListener("click", geometryButtonsClickHandler);
    polygonBtn.addEventListener("click", geometryButtonsClickHandler);

    function geometryButtonsClickHandler(event) {
      const geometryType = event.target.value;
      clearGeometry();
      sketchViewModel.create(geometryType);
    }
    let expandBuffer='' 

    this.$nextTick(() => {
      view.when(() => {
        const queryDiv = document.getElementById("queryDiv");
        if (queryDiv) {
            expandBuffer = new Expand({
            view: view,
            content: queryDiv,
            expandIcon: "rings",
            expandTooltip: "Afficher les options de buffer"
          });

          view.ui.add(expandBuffer, "top-right");

        } else {
          console.error("queryDiv n'a pas été trouvé.");
        }
      });
    });

    let bufferSize = 0;
    const bufferNum = document.getElementById("bufferNum");
    const bufferNumSlider = new Slider({
      container: bufferNum,
      min: 0,
      max: 5000,
      steps: 1,
      visibleElements: {
        labels: true
      },
      precision: 0,
      labelFormatFunction: (value) => {
        return `${value.toString()}m`;
      },
      values: [0]
    });
    // get user entered values for buffer
    bufferNumSlider.on(["thumb-change", "thumb-drag"], bufferVariablesChanged);
    function bufferVariablesChanged(event) {
      bufferSize = event.value;
      runQuery();
    }
    const clearGeometryBtn = document.getElementById("clearGeometry");
    clearGeometryBtn.addEventListener("click", clearGeometry);

    function clearGeometry() {
      sketchGeometry = null;
      sketchViewModel.cancel();
      sketchLayer.removeAll();
      bufferLayer.removeAll();

      clearSelectionStations()
    }

    function runQuery() {
      // Efface le buffer précédent
      bufferLayer.removeAll();

      // Vérifie si une géométrie existe et si la taille du buffer est supérieure à zéro
      if (sketchGeometry && bufferSize > 0) {
        // Crée la géométrie du tampon en utilisant geometryEngine.geodesicBuffer
        const bufferGeometry = geometryEngine.geodesicBuffer(sketchGeometry, bufferSize, "meters");

        // Crée un graphique pour afficher la géométrie du tampon avec un symbole de polygone par défaut
        const bufferGraphic = new Graphic({
          geometry: bufferGeometry,
          symbol: {
            type: "simple-fill", // symbole de remplissage simple
            color: [150, 150, 255, 0.4], // couleur avec transparence (bleu clair)
            outline: {
              color: [0, 0, 255], // contour bleu
              width: 2
            }
          }
        });

        // Ajoute le graphique du tampon à la couche bufferLayer
        bufferLayer.add(bufferGraphic);

        //surligner les stations qui se trouvent dans le tampon
        FilterStationsInGeometry(bufferGeometry); // Appel à la fonction pour surligner les stations

      }
    }
  document.getElementById("buffer").addEventListener("click", function() { expandBuffer.expanded=true})

    // Dashboard
    let expanddash=''
    this.$nextTick(() => {
      view.when(() => {
    const resultDiv = document.getElementById("resultDiv");
    if (resultDiv) {
          expanddash = new Expand({
            view: view,
            content: resultDiv,
            expandIcon: "dashboard",
            expandTooltip: "dashboard"
          });

          view.ui.add(expanddash, "top-right");

        } else {
          console.error("result n'a pas été trouvé.");
        }
    
         });
    });
     Chart.register(DoughnutController, ArcElement, Tooltip, Legend, Title);
   
    const canvasElement = document.getElementById('material-chart');


    // Création du graphique à secteurs
    const ctx = canvasElement.getContext('2d');
    const pieChart = new Chart(ctx, {
      type: 'doughnut',
      data: {
        labels: ['vide', 'bus_stop', 'ferry_terminal', 'airport', 'bus_station', 'taxi', 'railway_station', 'railway_halt', 'helipad', 'tram_stop'],
        datasets: [{
          data: [],  // Données initiales, mises à jour dans queryStatistics
          backgroundColor: [
           '#E74C3C', '#3498DB', '#2ECC71', '#F1C40F', '#9B59B6', '#E67E22', '#1ABC9C', '#34495E', '#D35400', '#7F8C8D'
          ]
        }]
      },
      options: {
    responsive: false,
    cutout: '35%',  
    plugins: {
      legend: {
        position: 'bottom',
        labels: {
          font: {
            size: 14  
          }
        }
      },
      title: {
        display: true,
        text: "Nombre de stations par type",
        font: {
          size: 16  
        }
      }
    }
  }
    });

    async function initializeChartWithAllStations() {
  await queryStatistics(); // Remplit la chart avec les valeurs par défaut
}
    
    async function queryStatistics() {
      const statDefinitions = [
        { onStatisticField: "CASE WHEN fclass = '' THEN 1 ELSE 0 END", outStatisticFieldName: "type_1_count", statisticType: "sum" },
        { onStatisticField: "CASE WHEN fclass = 'bus_stop' THEN 1 ELSE 0 END", outStatisticFieldName: "type_2_count", statisticType: "sum" },
        { onStatisticField: "CASE WHEN fclass = 'ferry_terminal' THEN 1 ELSE 0 END", outStatisticFieldName: "type_3_count", statisticType: "sum" },
        { onStatisticField: "CASE WHEN fclass = 'airport' THEN 1 ELSE 0 END", outStatisticFieldName: "type_4_count", statisticType: "sum" },
        { onStatisticField: "CASE WHEN fclass = 'bus_station' THEN 1 ELSE 0 END", outStatisticFieldName: "type_5_count", statisticType: "sum" },
        { onStatisticField: "CASE WHEN fclass = 'taxi' THEN 1 ELSE 0 END", outStatisticFieldName: "type_6_count", statisticType: "sum" },
        { onStatisticField: "CASE WHEN fclass = 'railway_station' THEN 1 ELSE 0 END", outStatisticFieldName: "type_7_count", statisticType: "sum" },
        { onStatisticField: "CASE WHEN fclass = 'railway_halt' THEN 1 ELSE 0 END", outStatisticFieldName: "type_8_count", statisticType: "sum" },
        { onStatisticField: "CASE WHEN fclass = 'helipad' THEN 1 ELSE 0 END", outStatisticFieldName: "type_9_count", statisticType: "sum" },
        { onStatisticField: "CASE WHEN fclass = 'tram_stop' THEN 1 ELSE 0 END", outStatisticFieldName: "type_10_count", statisticType: "sum" }
     
      ];

      const query = {
        where: "1=1", 
        outStatistics: statDefinitions
      };

      try {
        const result=await stations_transport.queryFeatures(query);
        const allStats=result.features[0].attributes;
        updateChart(pieChart, [
          allStats.type_1_count,
          allStats.type_2_count,
          allStats.type_3_count,
          allStats.type_4_count,
          allStats.type_5_count,
          allStats.type_6_count,
          allStats.type_7_count,
          allStats.type_8_count,
          allStats.type_9_count,
          allStats.type_10_count
        ]);
      } catch(data) {
        return console.error(data);
      }
    }

    function updateChart(chart, dataValues) {
      chart.data.datasets[0].data = dataValues;
      chart.update();
    }
    
    queryStatistics();
    document.getElementById("dashbord").addEventListener("click", function () {
      expanddash.expanded = true;
    });
    
      }
      }
    
</script> 
<style>
#viewDiv { 
  
  padding: 20px;
  width: 80%;
  position: fixed;
  top: 0;
  right: 0;
  height: 100%;
  box-sizing: border-box;
  
}
</style>
