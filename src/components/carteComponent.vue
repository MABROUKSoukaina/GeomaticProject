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
import BufferComponent from './bufferComponent.vue';
import SketchViewModel from "@arcgis/core/widgets/Sketch/SketchViewModel.js";
import Slider from "@arcgis/core/widgets/Slider.js";
import Graphic from "@arcgis/core/Graphic.js";
import PieChartMediaInfo from "@arcgis/core/popup/content/PieChartMediaInfo.js";
import ChartMediaInfoValue from "@arcgis/core/popup/content/support/ChartMediaInfoValue.js";
import MediaContent from "@arcgis/core/popup/content/MediaContent.js";
import Content from "@arcgis/core/popup/content/Content.js";
import Color from "@arcgis/core/Color.js";

export default {
  name: 'App',
  components: {
    controlComponent,
    BufferComponent
  },
  mounted() {
    esriConfig.apiKey = "AAPTxy8BH1VEsoebNVZXo8HurCMQWYp9lEfXja_mjt_bIbzSqzGQUgVTSWIF8MBcBnyoEf19M6itsQ-Tot8c5xKBeXVsNeWPMywDy5eapEuV96ycMkPd9le8odF8Hk2JI82G-tlJp_6C8pRpvpiXeerrHAC-Li_qsKHg7k3SLczWfPT-dTpB0MA8J80Q4o7FZmgyq7lS5j6EDjjR0PyWPjNLEyClIjh5F_KbyKvG6RKEn3Tyjx-U-RekcG8W5XvP3WQmgr8bKm-8AxmM6IFb9dRmrA..AT1_AGDmaCWp";
    const routeLayer = new RouteLayer();
    const map = new Map({
      basemap: "topo-vector",
      layers: [routeLayer]
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
   url: "https://services.arcgis.com/VyDAHz437VQUuqEU/arcgis/rest/services/provinces/FeatureServer/0",
   outFields: ["Nom"],
   popupTemplate: popupProvince,
   renderer: ProvinceRenderer,
         });

    map.add(provinces);

    const popupregion = {
          title: "la région {NOM_REG}",
        };
    const regions = new FeatureLayer({
       url: "https://services.arcgis.com/VyDAHz437VQUuqEU/arcgis/rest/services/regions/FeatureServer/0",
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
  url: "https://services.arcgis.com/VyDAHz437VQUuqEU/arcgis/rest/services/couche_transport/FeatureServer/0",
  outFields: ["name","fclass", "osm_id","id"], 
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
      url: "https://services.arcgis.com/VyDAHz437VQUuqEU/arcgis/rest/services/Polygones/FeatureServer/0",
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
    url: "https://services.arcgis.com/VyDAHz437VQUuqEU/arcgis/rest/services/Lignes/FeatureServer/0",
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
    url: "https://services.arcgis.com/VyDAHz437VQUuqEU/arcgis/rest/services/Points/FeatureServer/0",
    outFields: ["FID", "Nom", "Descpt"],
    popupTemplate: popupPoints,
    renderer:Pointrenderer

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

  var selectedPointrenderer = new SimpleRenderer({
  symbol: selectedpointSymbol
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
    })

  


 // Fonction principale pour gérer le filtrage par région et province
async function handleRegionChange(selectedRegion) {
    try {
        if (!selectedRegion) return;

        // 1. Définir le filtre pour la région et mettre à jour le rendu
        const filterValue = `NOM_REG='${selectedRegion}'`;
        regions.definitionExpression = filterValue;
        regions.renderer = SelectedRegionRenderer;
        await zoomToLayer(regions);

        // 2. Récupérer la géométrie de la région sélectionnée
        const regionGeometry = await getRegionGeometry(selectedRegion);
        
        // 3. Filtrer les stations à l'intérieur de la région
        await filterStationsInRegion(regionGeometry);

        // 4. Récupérer les provinces associées à la région
        await getProvinces(regionGeometry);

    } catch (err) {
        console.error("Erreur lors du traitement de la région sélectionnée : ", err);
    }
    }

async function handleProvinceChange(selectedProvince) {
    try {
        if (!selectedProvince) return;

        // 1. Définir le filtre pour la province et mettre à jour le rendu
        const filterValue = `Nom='${selectedProvince}'`;
        provinces.definitionExpression = filterValue;
        provinces.renderer = SelectedRegionRenderer;
        await zoomToLayer(regions);

        // 2. Récupérer la géométrie de la province sélectionnée
        const provinceGeometry = await getProvinceGeometry(selectedProvince);
        
        // 3. Filtrer les stations à l'intérieur de la province
        await filterStationsInProvince(provinceGeometry);

        
       

    } catch (err) {
        console.error("Erreur lors du traitement de la région sélectionnée : ", err);
    }
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

// Fonction pour filtrer les stations dans la région
async function filterStationsInRegion(regionGeometry) {
    const query = new Query();
    query.geometry = regionGeometry;
    query.spatialRelationship = "intersects";  // Utilisez "intersects" pour inclure les stations proches des frontières
    query.returnGeometry = false;
    query.outFields = ["id"];  // Récupérer uniquement l'ID

    const result = await stations_transport.queryFeatures(query);
    const stationsIds = result.features.map(feature => feature.attributes.id).join(', ');

    stations_transport.definitionExpression = `id IN (${stationsIds})`;
    stations_transport.renderer = selectedPointrenderer;
  console.log("Stations filtrées :", stationsIds);
   
    }
async function filterStationsInProvince(provinceGeometry) {
    const query = new Query();
    query.geometry = provinceGeometry;
    query.spatialRelationship = "intersects";  // Utilisez "intersects" pour inclure les stations proches des frontières
    query.returnGeometry = false;
    query.outFields = ["id"];  // Récupérer uniquement l'ID

    const result = await stations_transport.queryFeatures(query);
    const stationsIds = result.features.map(feature => feature.attributes.id).join(', ');

    stations_transport.definitionExpression = `id IN (${stationsIds})`;
    stations_transport.renderer = selectedPointrenderer;
    console.log("Stations filtrées :", stationsIds);
   
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

 let regionFilterExpression = ""; // Filtre basé sur la région géographique
  let typeFilterExpression = "";   // Filtre basé sur le type sélectionné

// selectType.addEventListener('change', function(event) {
//   const selectedType = event.target.value;

//   if (selectedType) {
//     // Définir le filtre de type en fonction de la sélection
//     typeFilterExpression = `fclass='${selectedType}'`;
//   } else {
//     // Effacer le filtre de type s'il n'y a pas de sélection
//     typeFilterExpression = "";
//   }

//   // Appliquer la définition combinée des filtres
  
// });

//   function applyCombinedFilter() {
//   // Construire la définition combinée
//   let combinedExpression = "";

//   if (regionFilterExpression && typeFilterExpression) {
//     combinedExpression = `${regionFilterExpression} AND ${typeFilterExpression}`;
//   } else if (regionFilterExpression) {
//     combinedExpression = regionFilterExpression;
//   } else if (typeFilterExpression) {
//     combinedExpression = typeFilterExpression;
//   }

//   // Appliquer l'expression de filtre combinée à la couche
//   stations_transport.definitionExpression = combinedExpression;

//   // Si vous avez un rendu spécifique pour les points filtrés
//   stations_transport.renderer = selectedPointrenderer;

//   // Optionnel : zoom sur les points filtrés
//   zoomToLayer(stations_transport);
// }
 

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


    // recherche par une geomertrie dessinée
  const graphicsLayer = new GraphicsLayer({ title: "graphicsLayer" });

  const sketch = new Sketch({
  view: view,
  layer: graphicsLayer
  });
const sketchExpand = new Expand({
      view: view,
      content: sketch,
      expanded: false,
      expandIcon:"pencil-mark"
      
    });
    view.ui.add(sketchExpand, "top-left");

  const editor = new Editor({
  view: view,
  });
  const editorExpand = new Expand({
      view: view,
      content: editor,
      expanded: false,
      
    });

    view.ui.add(editorExpand, "top-left");

  // afficher le sketch sur la carte une fois on presse le bouton avec id sketch
  document.getElementById("sketch").addEventListener("click", function () {
  sketchExpand.expanded= true;
  });

  document.getElementById("editer").addEventListener("click", function () {
  editorExpand.expanded=true
  });

 

// // rechercher par buffer
// const sketchLayer = new GraphicsLayer();
//         const bufferLayer = new GraphicsLayer();
// view.map.addMany([bufferLayer, sketchLayer]);
// let bufferSize = 0;
// view.ui.add([queryDiv], "bottom-left");

// let sketchGeometry = null;
//         const sketchViewModel = new SketchViewModel({
//           layer: sketchLayer,
//           defaultUpdateOptions: {
//             tool: "reshape",
//             toggleToolOnClick: false
//           },
//           view: view,
//           defaultCreateOptions: { hasZ: false }
//         });

//         sketchViewModel.on("create", (event) => {
//           if (event.state === "complete") {
//             sketchGeometry = event.graphic.geometry;
//             runQuery();
//           }
//         });

//         sketchViewModel.on("update", (event) => {
//           if (event.state === "complete") {
//             sketchGeometry = event.graphics[0].geometry;
//             runQuery();
//           }
//         });
//         // draw geometry buttons - use the selected geometry to sktech
//         const pointBtn = document.getElementById("point-geometry-button");
//         const lineBtn = document.getElementById("line-geometry-button");
//         const polygonBtn = document.getElementById("polygon-geometry-button");
//         pointBtn.addEventListener("click", geometryButtonsClickHandler);
//         lineBtn.addEventListener("click", geometryButtonsClickHandler);
//         polygonBtn.addEventListener("click", geometryButtonsClickHandler);
//         function geometryButtonsClickHandler(event) {
//           const geometryType = event.target.value;
//           clearGeometry();
//           sketchViewModel.create(geometryType);
//         }

//         const bufferNumSlider = new Slider({
//           container: "bufferNum",
//           min: 0,
//           max: 500,
//           steps: 1,
//           visibleElements: {
//             labels: true
//           },
//           precision: 0,
//           labelFormatFunction: (value, type) => {
//             return `${value.toString()}m`;
//           },
//           values: [0]
//         });
//         // get user entered values for buffer
//         bufferNumSlider.on(["thumb-change", "thumb-drag"], bufferVariablesChanged);
//         function bufferVariablesChanged(event) {
//           bufferSize = event.value;
//           runQuery();
//         }
//         // Clear the geometry and set the default renderer
//         const clearGeometryBtn = document.getElementById("clearGeometry");
//         clearGeometryBtn.addEventListener("click", clearGeometry);

//         // Clear the geometry and set the default renderer
//         function clearGeometry() {
//           sketchGeometry = null;
//           sketchViewModel.cancel();
//           sketchLayer.removeAll();
//           bufferLayer.removeAll();
//           clearHighlighting(); 
//         }

//         // set the geometry query on the visible 
//         const debouncedRunQuery = promiseUtils.debounce(() => {
//           if (!sketchGeometry) {
//             return;
//           }

//           resultDiv.style.display = "block";
//           updateBufferGraphic(bufferSize);
//         });

//         function runQuery() {
//           debouncedRunQuery().catch((error) => {
//             if (error.name === "AbortError") {
//               return;
//             }

//             console.error(error);
//           });
//         }

//         // Set the renderer with objectIds
//         let highlightHandle = null;
//         function clearHighlighting() {
//           if (highlightHandle) {
//             highlightHandle.remove();
//             highlightHandle = null;
//           }
//         }

//         
//         // update the graphic with buffer
//         function updateBufferGraphic(buffer) {
//           // add a polygon graphic for the buffer
//           if (buffer > 0) {
//             const bufferGeometry = geometryEngine.geodesicBuffer(sketchGeometry, buffer, "meters");
//             if (bufferLayer.graphics.length === 0) {
//               bufferLayer.add(
//                 new Graphic({
//                   geometry: bufferGeometry,
//                   symbol: sketchViewModel.polygonSymbol
//                 })
//               );
//             } else {
//               bufferLayer.graphics.getItemAt(0).geometry = bufferGeometry;
//             }
//           } else {
//             bufferLayer.removeAll();
//           }
//         }
//  //// Dashbord
 // Create the PieChartMediaInfo media type

const counts = {};
stations_transport.queryFeatures().then((result) => {
  result.features.forEach((feature) => {
    const type = feature.attributes["fclass"]; // Remplace par le nom exact de ton attribut
    counts[type] = (counts[type] || 0) + 1; // Compte le nombre de stations par type
    
  });

 
  // 2. Préparer les données pour le graphique
  const types = Object.keys(counts);
  const values = types.map(type => counts[type]);
   if (values) {console.log(values)} else console.log("objet vide")

  
  // Palette de 10 couleurs
  const colors = [
    [220, 123, 4, 1],   // Couleur 1
    [229, 80, 53, 1],   // Couleur 2
    [54, 162, 235, 1],  // Couleur 3
    [75, 192, 192, 1],  // Couleur 4
    [255, 206, 86, 1],  // Couleur 5
    [153, 102, 255, 1], // Couleur 6
    [255, 99, 132, 1],  // Couleur 7
    [255, 159, 64, 1],  // Couleur 8
    [199, 199, 199, 1],  // Couleur 9
    [0, 0, 0, 1]        // Couleur 10
  ]; // Ajuste les couleurs selon le nombre de types

  // 3. Créer le pieChartValue
  let pieChartValue = new ChartMediaInfoValue({
    colors :[
    new Color([220, 123, 4, 1]),   // Couleur 1
    new Color([229, 80, 53, 1]),   // Couleur 2
    new Color([54, 162, 235, 1]),  // Couleur 3
    new Color([75, 192, 192, 1]),  // Couleur 4
    new Color([255, 206, 86, 1]),  // Couleur 5
    new Color([153, 102, 255, 1]), // Couleur 6
    new Color([255, 99, 132, 1]),  // Couleur 7
    new Color([255, 159, 64, 1]),  // Couleur 8
    new Color([199, 199, 199, 1]),  // Couleur 9
    new Color([0, 0, 0, 1])        // Couleur 10
   ],
    fields: values.map((value, index) => `${types[index]}: ${value}`), // Formate les champs
    normalizeField: null,
  });
  console.log(pieChartValue.fields)

  // 4. Créer le PieChartMediaInfo
  let pieChart = new PieChartMediaInfo({
    title: "<b>Nombre de stations par type</b>",
    caption: "Distribution par type de station",
    value: pieChartValue
  });

  // 5. Créer le MediaContent
  let mediaElement = new MediaContent({
    mediaInfos: [pieChart]
  });
  const chartExpand = new Expand({
      view: view,
    content: mediaElement,
      expandIcon:"dashboard",
    expanded: false,
    });
  view.ui.add(chartExpand, "top-right");

 document.getElementById("dashbord").addEventListener("click", function () {
       chartExpand.expanded=true
});
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
