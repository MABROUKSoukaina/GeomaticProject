<template>
  <div>
    <div ref="controlDiv" id="viewDiv">
      
 
    </div>
  </div>
</template>
<script>
import controlComponent from './controlComponent.vue';
import esriConfig from "@arcgis/core/config.js";
import Map from '@arcgis/core/Map.js';
import MapView from '@arcgis/core/views/MapView.js';
import FeatureLayer from "@arcgis/core/layers/FeatureLayer.js";
import Search from '@arcgis/core/widgets/Search.js'
import Expand from "@arcgis/core/widgets/Expand.js";
import Editor from "@arcgis/core/widgets/Editor.js";
import Directions from "@arcgis/core/widgets/Directions.js";
import ScaleBar from "@arcgis/core/widgets/ScaleBar.js";
import Locate from "@arcgis/core/widgets/Locate.js";
import RouteLayer from "@arcgis/core/layers/RouteLayer.js";
import PopupTemplate from '@arcgis/core/PopupTemplate.js';
import SimpleMarkerSymbol from "@arcgis/core/symbols/SimpleMarkerSymbol.js";
import SimpleRenderer from "@arcgis/core/renderers/SimpleRenderer";
import SimpleLineSymbol from "@arcgis/core/symbols/SimpleLineSymbol";
import SimpleFillSymbol from "@arcgis/core/symbols/SimpleFillSymbol"; // Pour les polygones
import BasemapGallery from "@arcgis/core/widgets/BasemapGallery.js";
import LayerList from "@arcgis/core/widgets/LayerList.js";
import Query from "@arcgis/core/rest/support/Query.js";
import * as geometryEngine from "@arcgis/core/geometry/geometryEngine.js";
import Sketch from "@arcgis/core/widgets/Sketch.js";
import GraphicsLayer from "@arcgis/core/layers/GraphicsLayer.js";




export default {
  name: 'App',
  components: {
    controlComponent
  },
  mounted() {
    esriConfig.apiKey = "AAPTxy8BH1VEsoebNVZXo8HurIcKdRtoWHJb1v9VXl1gywugtAvel-QVM520Vso9ZllIYFWorcnobsBEfCKjCQACw677IpltmueO6ttMoTnjUf7b2DD8qZT9mMv7GmLX7yuFKNZZLzQLRiji059IGkrbVQ2lRD549Db6O9pkhiMPEMqb-AD5A91EKWmdJFziVOHPdYmRttYvEZohWua_3Qb6QBCZzcx03VPXHo_Qdq0YRYS_neWuth4dSllzYGh8eDvDwclJxC7ftK94lUI7IyPeSA..AT1_jfScL6A0";
    const routeLayer = new RouteLayer();
    const map = new Map({
      basemap: "arcgis-imagery",
      layers: [routeLayer]
    });

    const view = new MapView({
      map: map,
      center: [-7.092620, 31.791702],
      zoom: 5,
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

    var Stationsrenderer = new SimpleRenderer({
    symbol: new SimpleMarkerSymbol({
    color: "darkblue",
    size: 4
  })
    });

    var popupProvince = new PopupTemplate({
      title: "Province {Nom}",
   
  
});

 const provinces = new FeatureLayer({
   url: "https://services1.arcgis.com/DgoCfAVgBoJ0d01P/arcgis/rest/services/provinces/FeatureServer/0",
   outFields: ["Nom"],
   popupTemplate: popupProvince,
   renderer: ProvinceRenderer,
         });

    map.add(provinces);

    const popupregion = {
          title: "la région {NOM_REG}",
        };

    const regions = new FeatureLayer({
       url: "https://services1.arcgis.com/DgoCfAVgBoJ0d01P/arcgis/rest/services/regions/FeatureServer/0",
       outFields: ["NOM_REG"],
       popupTemplate: popupregion,
       renderer: RegionRenderer
    });
    map.add(regions);

  const popupstation = {
  title: "Information sur la station",
  content: "<p><span style='font-weight: bold; color: #222;'>Nom de la station</span> : {name}</p>" +
           "<p><span style='font-weight: bold; color: #222;'>Type de la station</span> : {fclass}</p>"
    };

const stations_transport = new FeatureLayer({
  url: "https://services1.arcgis.com/DgoCfAVgBoJ0d01P/arcgis/rest/services/transport_stations/FeatureServer",
  outFields: ["name","fclass", "osm_id"], 
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
      url: "https://services1.arcgis.com/DgoCfAVgBoJ0d01P/arcgis/rest/services/Polygones/FeatureServer/0",
      outFields: ["FID", "Nom", "Descrp"],
      popupTemplate: popupPolygones,
      renderer:Polygonerenderer
  });
    map.add(polygones);
  
  var popupLignes = new PopupTemplate({
      title: "Les informations du client",
      content: "<p><span style='font-weight: bold;'>Code</span> : {FID} </p>" +
      "<p> <span style='font-weight: bold;'>Nom</span> : {Nom} </p>" +
      "<p><span style='font-weight: bold;'>Déscription</span> : {Descrip} </p>"
  });

  // ajout de la couche des lignes
  const lignes = new FeatureLayer({
    url: "https://services1.arcgis.com/DgoCfAVgBoJ0d01P/arcgis/rest/services/Lignes/FeatureServer/0",
    outFields: ["FID", "Nom", "Descrip"],
    popupTemplate: popupLignes,
    renderer:Lignesrenderer
  });
    map.add(lignes);
    
    var popupPoints = new PopupTemplate({// Popup affichant les information sur les points crées
      title: "Information sur le point crée",
      content: "<p><span style='font-weight: bold;'>Code</span> : {FID} </p>" + "<p> <span style='font-weight: bold;'>Nom</span> : {Nom} </p>" + "<p><span style='font-weight: bold;'>Description</span> : {Descpt} </p>",
      
  });

  // ajout de la couche des points 
  const points = new FeatureLayer({
    url: "https://services1.arcgis.com/DgoCfAVgBoJ0d01P/arcgis/rest/services/Points/FeatureServer/0",
    outFields: ["FID", "Nom", "Descpt"],
    popupTemplate: popupPoints,
    renderer:Pointrenderer

  });
    map.add(points)



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
        content: new Editor({  // widgest de l'édition des couches (points, lignes, polygones)
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
      "top-right"
    );

    view.ui.add(
      new Expand({
        view: view,
        content: new Search({
          view: view
        })
      }),
      "top-right"
    );

    // widgest indiquant la localisation approchée
    const locate = new Locate({
      view: view,
      useHeadingEnabled: false,
      goToOverride: function (view, options) {
        options.target.scale = 1500;
        return view.goTo(options.target);
      }
    });
    view.ui.add(locate, "top-right");

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

    // charger le selecteur par les noms des provinces à partir de la couche province
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
    // charger le selecteur par les types de station à partir de la couche transport_station
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
      let option_vide = document.createElement("option");
      option_vide.value = "";
      option_vide.textContent = "vide";
      selectType.appendChild(option_vide);
    })

    // filtrer la couche station selon le type
    selectType.addEventListener('change', function (event) {
      const selectedtype = event.target.value;
      
        if (selectedtype) {
          const filterValue = `fclass='${selectedtype}'`;
          stations_transport.definitionExpression = filterValue
          zoomToLayer(stations_transport)
        }
        else {
          stations_transport.definitionExpression = null
        
        }
      });
    
    // Fonction pour appliquer le filtre sur la couche des stations
    async function filterStationsInRegion(regionGeometry) {
    // Créer une expression de filtre basée sur la géométrie
    var query = new Query();
    query.geometry = regionGeometry;
    query.spatialRelationship = "intersects";     //    juste une solution temporaire car Contains, similaire à within  récupère seulement les province avec appartenance total y compris les frontiéres, ce qui générer des problèmes d'affichage
    query.returnGeometry = false; // Nous n'avons pas besoin de la géométrie ici pour filtrer
    query.outFields = ["*"]; // Obtenir tous les champs ou spécifiez ceux qui sont nécessaires

    const result = await stations_transport.queryFeatures(query);
    // Récupérer les coordonnées sous forme de chaînes
const coordonnees = result.features.map(feature => `${feature.attributes.latitude}, ${feature.attributes.longitude}`);

// Afficher les identifiants de la couche station (on a utilisé les coordonnées car se sont les seuls attributs qui ont une valeur pour tous les stations)
console.log(coordonnees);

// Filtrer la couche affichée sur la carte
stations_transport.definitionExpression = `latitude IN (${coordonnees.join(', ')}) OR longitude IN (${coordonnees.join(', ')})`;

    // Filtrer la couche affichée sur la carte
     // Appliquer l'expression de définition
}

// Écoutez l'événement de changement de la région
selectRegion.addEventListener("change", function(event) {
    var selectedRegion = event.target.value;
  if (selectedRegion) {

          const filterValue = `NOM_REG='${selectedRegion}'`;
          regions.definitionExpression = filterValue
          zoomToLayer(regions)
        
        // Récupérer la géométrie de la région sélectionnée
        getRegionGeometry(selectedRegion)
            .then(function(regionGeometry) {
                return filterStationsInRegion(regionGeometry); // Filtrer les stations à l'intérieur de la région
            })
            .catch(function(err) {
                console.error("Erreur lors de la récupération des données : ", err);
            });
    } else {
        // Si aucune région n'est sélectionnée, réinitialiser le filtre
    stations_transport.definitionExpression = ""; // Afficher toutes les stations
    regions.definitionExpression = "";
    }
});


    // récuppérer la géometrie de la région séléctionnée
    async function getRegionGeometry(regionNom) {
    var query = new Query();
    query.where = `NOM_REG = '${regionNom}'`; 
    query.returnGeometry = true;

    const result = await regions.queryFeatures(query);
      if (result.features.length > 0) {
      console.log("fonction get region works")
      return result.features[0].geometry; // Récupère la géométrie de la première région trouvée
      
    } else {
      throw new Error("Aucune région trouvée avec ce nom.");
    }
  }
   // Fonction pour récupérer les provinces
 async function getProvinces(regionGeometry) {
    var query = new Query();
    query.geometry = regionGeometry;
    query.spatialRelationship = "within"; // Relation spatiale
    query.returnGeometry = false;
    query.outFields = ["Nom"]; 

   const result = await provinces.queryFeatures(query);
    if (result!=null) { console.log("Province recupérés" ,result)} else console.log("aucun province récupéré")
    selectProvince.innerHTML = ""; // Efface les anciennes options

   result.features.forEach(function (feature) {
      console.log(feature.attributes["Nom"])
      var option = document.createElement("option");
      option.value = feature.attributes["Nom"]; // Valeur de la province
      option.text = feature.attributes["Nom"]; // Texte affiché
      selectProvince.add(option);
    });

    }

    //Selectionner les provinces qui appartiennent à une region
    
    selectRegion.addEventListener("change", function(event) {
    var selectedRegion = event.target.value;
    console.log(selectedRegion)
    if (selectedRegion) {
      // Récupère la géométrie de la région sélectionnée et les provinces associées
      getRegionGeometry(selectedRegion)
        .then(function(regionGeometry) {
          return getProvinces(regionGeometry);
        })
        .catch(function(err) {
          console.error("Erreur lors de la récupération des données : ", err);
        });
        zoomToLayer(stations_transport)
    }
    });

    // supprimer tous les filtres
  const InitDiv = document.getElementById("initialiser");
    function Actualiser() {
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

    // recherche par une geomertrie dessinee

  const graphicsLayer = new GraphicsLayer({ title: "graphicsLayer" });

  const sketch = new Sketch({
  view: view,
  layer: graphicsLayer
  });

  // afficher le sketch sur la carte une fois on presse le bouton avec id sketch
    document.getElementById("sketch").addEventListener("click", function () {
    
     view.ui.add(
      new Expand({
        view: view,
        content: sketch,
        expanded: true,
      }),
      "top-right"
    );
  });
  }
};
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
