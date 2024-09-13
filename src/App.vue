
<template>
  <div>
  <controlComponent/>
  <carteComponent/>
  </div>
</template>
<script >
import carteComponent from './components/carteComponent.vue'
import controlComponent from './components/controlComponent.vue'
import esriConfig from "@arcgis/core/config.js";
import Map from '@arcgis/core/Map.js';
import MapView from '@arcgis/core/views/MapView.js';
import FeatureLayer from "@arcgis/core/layers/FeatureLayer.js";
import Search from '@arcgis/core/widgets/Search.js';
import Renderer from '@arcgis/core/renderers/Renderer.js';
import PopupTemplate from '@arcgis/core/PopupTemplate.js';
import BasemapToggle from "@arcgis/core/widgets/BasemapToggle.js";
import Legend from "@arcgis/core/widgets/Legend.js";
import Home from "@arcgis/core/widgets/Home.js";
import Expand from "@arcgis/core/widgets/Expand.js";
import Fullscreen from "@arcgis/core/widgets/Fullscreen.js";
import Editor from "@arcgis/core/widgets/Editor.js";
import Query from "@arcgis/core/rest/support/Query.js";
import RouteLayer from "@arcgis/core/layers/RouteLayer.js";
import Directions from "@arcgis/core/widgets/Directions.js";
import ScaleBar from "@arcgis/core/widgets/ScaleBar.js";
import FeatureSet from "@arcgis/core/rest/support/FeatureSet.js";
import Graphic from "@arcgis/core/Graphic.js";
import Point from "@arcgis/core/geometry/Point.js";
import RouteParameters from "@arcgis/core/rest/support/RouteParameters.js";
import * as route from "@arcgis/core/rest/route.js";
import GraphicsLayer from "@arcgis/core/layers/GraphicsLayer.js";
import Stop from "@arcgis/core/rest/support/Stop.js";
import Collection from "@arcgis/core/core/Collection.js";
import SimpleMarkerSymbol from "@arcgis/core/symbols/SimpleMarkerSymbol.js";
import SimpleLineSymbol from "@arcgis/core/symbols/SimpleLineSymbol.js";
import Locate from "@arcgis/core/widgets/Locate.js";
import Print from "@arcgis/core/widgets/Print.js";

export default {
  name: 'App',
  components: {
    carteComponent,
    controlComponent
  },
  mounted() {
    
    esriConfig.apiKey = "AAPK24bf2835c14d4c26ab089b2bdc27a9fccn9xAcTlgeNhD7JGVAc8Xji4AWYFk0dfIr8TT6GdZKry3v47kjF0LSAtsiipAMuA";
    const routeLayer = new RouteLayer();
    const map = new Map({
      basemap: "arcgis-navigation", // Basemap layer service
      layers: [routeLayer]
    });
    const view = new MapView({
      map: map,
      center: [-7.092620, 31.791702], // Longitude, latitude
      zoom: 5.8, // Zoom level
      container: "viewDiv", // Div element
      layers: [routeLayer]
    });
    const basemapToggle = new BasemapToggle({
      view: view,
      nextBasemap: "satellite"
    });
    view.ui.add(basemapToggle, "bottom-left")
    
    view.ui.add(
      new Expand({
        view: view,
        content: new Legend({
          view: view,
          style: "card",
        }),
        expanded: true,

      }),
      "top-left"
    );

    
    
    var popupClient = new PopupTemplate({
      title: " <span style='font-weight: bold;'>Les informations du client</span>",
      content: "<p><span style='font-weight: bold;'>Code</span> : {CODE_AGENC} </p>" + "<p> <span style='font-weight: bold;'>Nom</span> : {NOM_AGENCE} </p>" + "<p><span style='font-weight: bold;'>Ville</span> : {VILLE_COMM} </p>" + "<p> <span style='font-weight: bold;'>Adresse</span> : {ADRESSE} </p>" + "<p> <span style='font-weight: bold;'>Télephone</span> : {TEL_DCA} </p>"
    });
    
    var highlightSymbol = {
      type: "simple-marker",
      color: "darkblue", // Couleur bleu foncé
      size: 20,
      outline: {
        color: "lightblue", // Couleur bleu clair pour le contour
        width: 10
      }
    };
    var symbole_pt_visité = {
      type: "simple-marker",
      style:"triangle",
      color: "red", // Couleur bleu foncé
      size: 20,
      outline: {
        color: "pink", // Couleur bleu clair pour le contour
        width: 2
      }
    };
    var symbole_pt_visité_tout = {
      type: "simple-marker",
      color: "red", // Couleur bleu foncé
      size: 16,
      outline: { // autocasts as new SimpleLineSymbol()
        color: "pink",
        width: 6
      },
    };
    let regionRenderer = {
      type: "unique-value",  // autocasts as new UniqueValueRenderer()
      field: "REGION",

      uniqueValueInfos: [{
        // All features with value of "North" will be blue
        value: "AGADIR",
        symbol: {
          type: "simple-marker", // autocasts as new SimpleFillSymbol()
          style: "solid",
          outline: { // autocasts as new SimpleLineSymbol()
            color: "black",
            width: 1
          },
          color: "purple",
          size: 5// Transparent color
        }
      },
      {
        // All features with value of "North" will be blue
        value: "CASABLANCA",
        symbol: {
          type: "simple-marker", // autocasts as new SimpleFillSymbol()
          style: "solid",
          outline: { // autocasts as new SimpleLineSymbol()
            color: "black",
            width: 1
          },
          color: "yellow",
          size: 5// Transparent color
        }
      },
      {
        // All features with value of "North" will be blue
        value: "FES",
        symbol: {
          type: "simple-marker", // autocasts as new SimpleFillSymbol()
          style: "solid",
          outline: { // autocasts as new SimpleLineSymbol()
            color: "black",
            width: 1
          },
          color: "red",
          size: 5// Transparent color
        }
      },
      {
        // All features with value of "North" will be blue
        value: "SETTAT",
        symbol: {
          type: "simple-marker", // autocasts as new SimpleFillSymbol()
          style: "solid",
          outline: { // autocasts as new SimpleLineSymbol()
            color: "black",
            width: 1
          },
          color: "blue",
          size: 5// Transparent color
        }
      },
      {
        // All features with value of "North" will be blue
        value: "RABAT",
        symbol: {
          type: "simple-marker", // autocasts as new SimpleFillSymbol()
          style: "solid",
          outline: { // autocasts as new SimpleLineSymbol()
            color: "black",
            width: 1
          },
          color: "green",
          size: 5// Transparent color
        }
      },
      {
        // All features with value of "North" will be blue
        value: "TANGER",
        symbol: {
          type: "simple-marker", // autocasts as new SimpleFillSymbol()
          style: "solid",
          outline: { // autocasts as new SimpleLineSymbol()
            color: "black",
            width: 1
          },
          color: "brown",
          size: 5// Transparent color
        }
      },
      {
        // All features with value of "North" will be blue
        value: "MARRAKECH",
        symbol: {
          type: "simple-marker", // autocasts as new SimpleFillSymbol()
          style: "solid",
          outline: { // autocasts as new SimpleLineSymbol()
            color: "black",
            width: 1
          },
          color: "orange",
          size: 5// Transparent color
        }
      },
      {
        // All features with value of "North" will be blue
        value: "MEKNES",
        symbol: {
          type: "simple-marker", // autocasts as new SimpleFillSymbol()
          style: "solid",
          outline: { // autocasts as new SimpleLineSymbol()
            color: "black",
            width: 1
          },
          color: "lightgreen",
          size: 5// Transparent color
        }
      },
      {
        // All features with value of "North" will be blue
        value: "OUJDA",
        symbol: {
          type: "simple-marker", // autocasts as new SimpleFillSymbol()
          style: "solid",
          outline: { // autocasts as new SimpleLineSymbol()
            color: "black",
            width: 1
          },
          color: "lightblue",
          size: 5// Transparent color
        }
      }],


    }

    let scaleBar = new ScaleBar({
      view: view
    });
    // Add widget to the bottom left corner of the view
    view.ui.add(scaleBar, {
      position: "bottom-right"
    });

    const postes_client = new FeatureLayer({
      url: "https://services2.arcgis.com/MX1YTHWGW4mL3eoF/arcgis/rest/services/poste_client/FeatureServer/0",
      outFiealds: ["CODE_AGENC", "NOM_AGENCE", "VILLE_COMM", "ADRESSE", "TEL_DCA"],
      popupTemplate: popupClient,
      renderer: regionRenderer
    });
    postes_client.title = "Mes clients",
      //map.add(maroc);
      map.add(postes_client);
      view.ui.add(
      new Expand({
        view: view,
        content: new Editor({
          view: view
        })
      }),
      "top-left"
    );
    view.ui.add(
      new Expand({
        view: view,
        content: new Directions({
          view: view,
          layer: routeLayer
        })
      }),
      "top-left"
    );
   
    const searchWidget1= new Search({
      view: view, // La vue de la carte où ajouter le widget
      allPlaceholder: "Rechercher des clients...",
      sources: [

        {
          layer: postes_client, // Remplacez 'votreCouche1' par votre couche réelle
          searchFields: ["CODE_AGENC", "VILLE_COMM", "REGION", "ADRESSE", "Latitude", "Longitude", "NOM_AGENCE", "TEL_DCA"],
          displayField: "VILLE_COMM", // Champs à afficher dans les résultats de recherche
          exactMatch: false,
          outFields: ["*"], // Renvoyer tous les attributs de l'entité
          name: "rechercher par région", // Nom affiché dans le menu de recherche
          placeholder: "Rechercher par région " // Placeholder dans la boîte de recherche
        },
         {
          layer: postes_client, // Remplacez 'votreCouche1' par votre couche réelle
          searchFields: ["CODE_AGENC", "VILLE_COMM", "REGION", "ADRESSE", "Latitude", "Longitude", "NOM_AGENCE", "TEL_DCA"],
          displayField: "VILLE_COMM", // Champs à afficher dans les résultats de recherche
          exactMatch: false,
          outFields: ["*"], // Renvoyer tous les attributs de l'entité
          name: "rechercher par ville", // Nom affiché dans le menu de recherche
          placeholder: "Rechercher par ville " // Placeholder dans la boîte de recherche
        },
         {
          layer: postes_client, // Remplacez 'votreCouche1' par votre couche réelle
          searchFields: ["CODE_AGENC", "NOM_AGENCE", "REGION", "ADRESSE", "Latitude", "Longitude", "NOM_AGENCE", "TEL_DCA"],
          displayField: "NOM_AGENCE", // Champs à afficher dans les résultats de recherche
          exactMatch: false,
          outFields: ["*"], // Renvoyer tous les attributs de l'entité
          name: "rechercher par nom", // Nom affiché dans le menu de recherche
          placeholder: "Rechercher par nom " // Placeholder dans la boîte de recherche
        },
        {
          layer: postes_client, // Remplacez 'votreCouche1' par votre couche réelle
          searchFields: ["CODE_AGENC", "NOM_AGENCE", "REGION", "ADRESSE", "Latitude", "Longitude", "TEL_DCA"],
          displayField: "ADRESSE", // Champs à afficher dans les résultats de recherche
          exactMatch: false,
          outFields: ["*"], // Renvoyer tous les attributs de l'entité
          name: "rechercher par adresse", // Nom affiché dans le menu de recherche
          placeholder: "Rechercher par adresse " // Placeholder dans la boîte de recherche
        },
        {
          layer: postes_client, // Remplacez 'votreCouche1' par votre couche réelle
          searchFields: ["CODE_AGENC", "NOM_AGENCE", "REGION", "ADRESSE", "Latitude", "Longitude", "TEL_DCA"],
          displayField: "CODE_AGENC", // Champs à afficher dans les résultats de recherche
          exactMatch: false,
          outFields: ["*"], // Renvoyer tous les attributs de l'entité
          name: "rechercher par code", // Nom affiché dans le menu de recherche
          placeholder: "Rechercher par code " // Placeholder dans la boîte de recherche
        },
        
      ]
    });
    view.ui.add(searchWidget1, {
        position: "top-right"
    });
      view.ui.add(
      new Expand({
        view: view,
        content: new Search({
          view: view
        })
      }),
      "top-right"
      );
    view.ui.add(
      new Home({
        view: view
      }),
      "top-right"
    );
    view.ui.add(
      new Fullscreen({
        view: view
      }),
      "top-right"
    );
    const locate = new Locate({
      view: view,
      useHeadingEnabled: false,
      goToOverride: function (view, options) {
        options.target.scale = 1500;
        return view.goTo(options.target);
      }
    });
    view.ui.add(locate, "top-right");
    view.ui.add(
      new Expand({
        view: view,
        content: new Print({
          view: view,
          // specify your own print service
          printServiceUrl:
            "https://utility.arcgisonline.com/arcgis/rest/services/Utilities/PrintingTools/GPServer/Export%20Web%20Map%20Task"
        })
      }),
      "top-right"
    );

    const coordinatesWidget = document.createElement("div");
    coordinatesWidget.id = "coordinatesWidget";

    // Ajoutez le widget de coordonnées à la vue
    view.ui.add(coordinatesWidget, "bottom-left");

    // Mettez à jour les coordonnées dans le widget lors du déplacement de la souris sur la carte
    view.on("pointer-move", (event) => {
      const coords = view.toMap({ x: event.x, y: event.y });
      const longitude = coords.longitude.toFixed(6);
      const latitude = coords.latitude.toFixed(6);

      coordinatesWidget.textContent = `Longitude: ${longitude}, Latitude: ${latitude}`;
    });
    const selectRegion = document.createElement("select");
    const selectville = document.createElement("select");
    const defaultOption = document.createElement("option");
    defaultOption.value = "default";
    defaultOption.textContent = "Choisir une destination de départ";
    // defaultOption.disabled = true;
    // defaultOption.selected = true;
    // defaultOption.hidden = true; 
    selectville.insertBefore(defaultOption, selectville.firstChild);
    selectville.classList.add("custom-select");
    const addedRegions = new Set();
    const REGION = {
      where: '1=1',
      outFields: ["REGION"], // Attributes to return
      returnGeometry: true
    }
    postes_client.queryFeatures(REGION).then((results) => {
      const defaultOption1 = document.createElement("option");
      defaultOption1.value = "choisir";
      defaultOption1.textContent = "choisir votre région";
      // defaultOption1.disabled = true;
      // defaultOption1.selected = true;
      defaultOption1.hidden = true;
      selectRegion.add(defaultOption1);
      const features = results.features;
      for (const feature of features) {
      const region = feature.attributes["REGION"];
        if (!addedRegions.has(region)) {
          let option = document.createElement("option");
          option.value = region;
          option.textContent = region;
          selectRegion.appendChild(option);
          addedRegions.add(region);
        }
      }
      let option = document.createElement("option");
      option.value = "tout";
      option.textContent = "tout";
      selectRegion.appendChild(option);
    }),
    selectRegion.classList.add("custom-select");
    selectville.classList.add("custom-select");
    function setFeatureLayerFilter1(expression) {
    postes_client.definitionExpression = expression;
    }
    var Table_coord_tot = []
    var coord_vers = []
    var points_passés=[]
    function insert_tot(value1, value2) {
      Table_coord_tot.push([value1, value2]);
    }
    function remove_tot(value1, value2) {
      for (let i = 0; i < Table_coord_tot.length; i++) {
        const row = Table_coord_tot[i];
        if (row[0] === value1 && row[1] === value2) {
          Table_coord_tot.splice(i, 1); 
        }
      }
    }
      selectRegion.addEventListener('change', function (event) {
      const selectedRegion = event.target.value;
      if (selectedRegion === "tout") {
        postes_client.definitionExpression = null;
        postes_client.refresh();
        postes_client.renderer =regionRenderer;
        zoomToLayer(postes_client)
      }
      else {
        if (selectedRegion) {
          const filterValue = `REGION='${selectedRegion}'`;
          colorer()
          setFeatureLayerFilter1(filterValue);
          zoomToLayer(postes_client)
          const selectedValue = selectRegion.value;
          console.log(selectedValue);
          Table_coord_tot = []
          selectville.innerHTML = '';
          const villes = {
            where: "REGION = '" + selectedValue + "'",  // Set by select element
            outFields: ["VILLE_COMM", "ADRESSE", "Latitude", "Longitude"], // Attributes to return
            returnGeometry: true
          }
           postes_client.queryFeatures(villes).then((results) => {
            const features = results.features;
            const defaultOption = document.createElement("option");
            defaultOption.value = "default";
            defaultOption.textContent = "Choisir une destiantion de départ";
            defaultOption.disabled = true;
            defaultOption.selected = true;
            defaultOption.hidden = true;
            selectville.insertBefore(defaultOption, selectville.firstChild);
              for (const feature of features) {
              const cityName = feature.attributes["VILLE_COMM"];
              const address = feature.attributes["ADRESSE"];
              const Latitude = feature.attributes["Latitude"];
              const Longitude = feature.attributes["Longitude"]
              const option = document.createElement("option");
              option.value = address;
              option.innerHTML = "<span style='font-weight: bold'>" + cityName + "</span> : " + address;
              ;
              selectville.appendChild(option);
              insert_tot(Latitude, Longitude)
              coord_vers = Table_coord_tot
            } console.log(coord_vers)
          }
          )
        }
        else {
          setFeatureLayerFilter1(null);
        }
        }
      });     
    let storedPointDestination = null;
    let lat_actuel = null;               
    let long_actuel = null;
    let adresse_cible = null;
    let selectedAdresse = null;
    selectville.addEventListener('change', function (event) {   
      selectedAdresse = event.target.value;
      console.log(selectedAdresse);
      colorer(selectedAdresse) 
    
    });
     let minDistance = Infinity;
    async function findNearestPoint(Table_coord_tot, endLatitude, endLongitude) {
      try {
        
        let nearestPoint = null;
        for (const dest of Table_coord_tot) {
          const [destLat, destLon] = dest;
          console.log("Current lat:", destLat, "Current lon:", destLon);
          const distToEnd = await solveRoute(endLongitude, endLatitude, destLon, destLat);
          if (distToEnd < minDistance && distToEnd > 0) {
            minDistance = distToEnd;
            nearestPoint = dest;
          }
          console.log(distToEnd)
          console.log(minDistance)
        }
        return nearestPoint;
      }
      catch (error){
        console.log("une erreur est produite lors de la résolution du trajet", error)
        throw error;
      }
      }
    const layervisited = new GraphicsLayer();
    map.add(layervisited);
    const routeLayer1 = new GraphicsLayer();
    map.add(routeLayer1)
    async function solveRoute(startLongitude, startLatitude, endLongitude, endLatitude) {
      try {
      const stops = new Collection([
        new Stop({
          geometry: { x: startLongitude, y: startLatitude },
        }),
        new Stop({
          geometry: { x: endLongitude, y: endLatitude },
        })
      ]);
      const routeParams = new RouteParameters({
        apiKey: "AAPK24bf2835c14d4c26ab089b2bdc27a9fccn9xAcTlgeNhD7JGVAc8Xji4AWYFk0dfIr8TT6GdZKry3v47kjF0LSAtsiipAMuA",
        stops
      });
      const routeUrl = "https://route-api.arcgis.com/arcgis/rest/services/World/Route/NAServer/Route_World";
      
        const routeSolveResult = await route.solve(routeUrl, routeParams);
        const distance = Math.round(routeSolveResult.routeResults[0].route.attributes.Total_Kilometers * 1000) / 1000;
        return distance;
      } catch (error) {
        console.error("Erreur lors de la résolution du trajet :", error);
        throw error; 
      }
    }  
    // (async () => {
    //   try {
    //     const test = await solveRoute(-7.7783462, 30.7415341, -10.0527498, 28.9883659);
    //     console.log(test);
    //   } catch (error) {
    //     console.error("Erreur lors de l'exécution de solveRoute :", error);
    //   }
    // })();
    const filterDiv = document.getElementById("control");
    filterDiv.appendChild(selectRegion);
    const filterville = document.getElementById("client");
    filterville.appendChild(selectville);
    function zoomToLayer(layer) {
      return layer.queryExtent().then((response) => {
        view.goTo(response.extent)
          .catch((error) => {
            console.error(error);
          });
      });
    } 
    function Demarrer() {
      const myModal = new bootstrap.Modal(document.getElementById("myModal"));
      if (!selectedAdresse) {
        myModal.show();
        return;
      }
      else {
        try {
          if (Table_coord_tot.length > 0) {
            const coordonnees = {
              where: "ADRESSE = '" + selectedAdresse + "'",  // Set by select element
              outFields: ["Latitude", "Longitude"], // Attributes to return
              returnGeometry: true
            }
            postes_client.queryFeatures(coordonnees).then((results) => {
              const features = results.features;
              for (const feature of features) {
                const latitude = feature.attributes["Latitude"]; lat_actuel = latitude;
                const Longitude = feature.attributes["Longitude"]; long_actuel = Longitude;
                remove_tot(lat_actuel, long_actuel)
                points_passés.push([latitude, Longitude])
                console.log(latitude, Longitude)

              }
              findNearestPoint(Table_coord_tot, lat_actuel, long_actuel).then((point_destnation) => {
                storedPointDestination = point_destnation
                const [lat_dest, long_dest] = storedPointDestination;
                console.log(storedPointDestination);
                const point_proche_query = {
                  where: "Latitude = '" + lat_dest + "' AND Longitude = '" + long_dest + "'",   // Set by select element
                  outFields: ["ADRESSE"], // Attributes to return
                  returnGeometry: true
                }
                postes_client.queryFeatures(point_proche_query).then((results) => {
                  const features = results.features;
                  for (const feature of features) {
                    adresse_cible = feature.attributes["ADRESSE"]
                    console.log(adresse_cible)
                    const modalBody = document.querySelector("#myModal .modal-body");
                    modalBody.innerHTML = "Votre prochaine destination est : <strong>" + adresse_cible + " "+ "( environ de "+minDistance+" "+"Km"+")"+"</strong>";
                    myModal.show();
                  }
                  colorer_dest(selectedAdresse, adresse_cible);
                  selectedAdresse = adresse_cible
                  itineraire(lat_actuel, long_actuel, lat_dest, long_dest)
                  routeLayer1.removeAll()
                  minDistance = Infinity;
                });
              });
            });
          }
          else {
            const terminer_pop = new bootstrap.Modal(document.getElementById("terminer_pop"));
            terminer_pop.show();
            return;

          }
        }
        catch(error){console.log("probleme de resolusion de trajet",error)}
    }
    
    }
    function colorer(value) { 
      postes_client.renderer = {
        type: "unique-value",  // autocasts as new UniqueValueRenderer()
        field: "ADRESSE",
        defaultSymbol: {
          type: "simple-marker", // autocasts as new SimpleMarkerSymbol()
          size: 10,
          color: "green",
          outline: { // autocasts as new SimpleLineSymbol()
            width: 2,
            color: "lightgreen"
          }
        },  // autocasts as new SimpleFillSymbol()
        uniqueValueInfos: [{
          // All features with value of "North" will be blue
          value: value,
          symbol: highlightSymbol
        }]
      }
    }
    function colorer_dest( value, adresse_cible) {
      postes_client.renderer = {
        type: "unique-value",  // autocasts as new UniqueValueRenderer()
        field: "ADRESSE",
        defaultSymbol: {
          type: "simple-marker", // autocasts as new SimpleMarkerSymbol()
          size: 14,
          color: "green",
          outline: { // autocasts as new SimpleLineSymbol()
            width: 2,
            color: "lightgreen"
          }
        }, // autocasts as new SimpleFillSymbol()
        uniqueValueInfos: [{
           value: value,
          symbol: symbole_pt_visité_tout
         },
        {
          value: adresse_cible,
          symbol: highlightSymbol
        }]
      } 
      for (let i = 0; i < points_passés.length - 1; i++) {
        
        const coordonnees = points_passés[i];
        const latitude = coordonnees[0];
        const longitude = coordonnees[1];

        const point = new Point({
          y: latitude,
          x: longitude
        });

        const graphic = new Graphic({
          geometry: point,
          symbol: symbole_pt_visité_tout
        });
        layervisited.graphics.add(graphic);
     
      }
    }
    async function itineraire(startLatitude, startLongitude, endLatitude, endLongitude) {
      const stops = new Collection([
        new Stop({
          geometry: { x: startLongitude, y: startLatitude },
        }),
        new Stop({
          geometry: { x: endLongitude, y: endLatitude },
        })
      ]);
      const routeParams = new RouteParameters({
        apiKey: "AAPK24bf2835c14d4c26ab089b2bdc27a9fccn9xAcTlgeNhD7JGVAc8Xji4AWYFk0dfIr8TT6GdZKry3v47kjF0LSAtsiipAMuA",
        stops
      });
      const routeUrl = "https://route-api.arcgis.com/arcgis/rest/services/World/Route/NAServer/Route_World";
      const routeSolveResult = await route.solve(routeUrl, routeParams);
      const routeGeometry = routeSolveResult.routeResults[0].route.geometry;
      const routeSymbol = new SimpleLineSymbol({
        color: [0, 0, 255],
        width: 3
      });
      const routeGraphic = new Graphic({
        geometry: routeGeometry,
        symbol: routeSymbol
      });
      routeLayer1.add(routeGraphic);

    }
    const InitDiv = document.getElementById("initialiser");
    function Actualiser() {
      postes_client.definitionExpression = null;
      postes_client.refresh();
      postes_client.renderer = regionRenderer;
      zoomToLayer(postes_client)
      routeLayer1.removeAll()
      selectRegion.selectedIndex = 0;
      selectville.selectedIndex = 0;
      while (selectville.options.length > 1) {
        selectville.remove(1);
      }

    }
    InitDiv.addEventListener('click', function () {
      Actualiser(); 
    });
    const Demdiv = document.getElementById("demarrer")
    Demdiv.addEventListener('click', function () {
    Demarrer();
    });
  },
}    
</script>                                     
<style>
.custom-select {
  background-color: #fcfdff;
  color: #050505;
  border: 1px solid #491deb;
  margin-bottom: 0px;
  border-radius: 4px;
  font-size: 14px;
  width: 100%;
  height: 40px;
  color: #000000;
}

</style> 
