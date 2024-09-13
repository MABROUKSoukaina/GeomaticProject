<template>
  <div>
    <div ref="controlDiv" id="viewDiv">
    </div>
  </div>
</template>
<script>
// import esriConfig from "@arcgis/core/config.js";
// import Map from '@arcgis/core/Map.js'; 
// import MapView from '@arcgis/core/views/MapView.js'; 
// import FeatureLayer from "@arcgis/core/layers/FeatureLayer.js";
// import Search from '@arcgis/core/widgets/Search.js'; 
// import Renderer from '@arcgis/core/renderers/Renderer.js'; 
// import PopupTemplate from '@arcgis/core/PopupTemplate.js'; 
// import BasemapToggle from "@arcgis/core/widgets/BasemapToggle.js";
// import Legend from "@arcgis/core/widgets/Legend.js";
// import Home from "@arcgis/core/widgets/Home.js";
// import Expand from "@arcgis/core/widgets/Expand.js";
// import Fullscreen from "@arcgis/core/widgets/Fullscreen.js";
// import Editor from "@arcgis/core/widgets/Editor.js";
// import FeatureTableVM from "@arcgis/core/widgets/FeatureTable/FeatureTableViewModel.js";
// import FeatureTable from "@arcgis/core/widgets/FeatureTable.js";
// import SimpleMarkerSymbol from "@arcgis/core/symbols/SimpleMarkerSymbol.js";

//     esriConfig.apiKey = "AAPK24bf2835c14d4c26ab089b2bdc27a9fccn9xAcTlgeNhD7JGVAc8Xji4AWYFk0dfIr8TT6GdZKry3v47kjF0LSAtsiipAMuA";
//     const map = new Map({
        
//       basemap:"topo-vector" // Basemap layer service
//       });
//       const view = new MapView({
//       map: map,
//       center: [-7.092620, 31.791702 ], // Longitude, latitude
//       zoom: 6, // Zoom level
//       container: "viewDiv" // Div element
//       });
//       const basemapToggle = new BasemapToggle({
//           view: view,
//           nextBasemap: "satellite"
//           });
//       const view2=new MapView({
      
//       container: "control",
        
//       })
//       view.ui.add(basemapToggle,"bottom-left")
//       const searchWidget = new Search({
//         view: view
//       });
//       view.ui.add(
//             new Expand({
//               view: view,
//               content: new Search({
//                 view: view
//               })
//             }),
//             "top-left"
//           );
//       // Adds the search widget below other elements in
//       // the top left corner of the view
      
     
//         view.ui.add(
//             new Expand({
//               view: view,
//               content: new Legend({
//                 view: view
//               })
//             }),
//             "top-left"
//           );
//           view.ui.add(
//             new Home({
//               view: view
//             }),
//             "top-left"
//           );
//           view.ui.add(
//             new Fullscreen({
//               view: view
//             }),
//             "top-left"
//           );      
//       var popupClient = new PopupTemplate({
//           title: " <span style='font-weight: bold;'>Les informations du client</span>",
//           content:"<p><span style='font-weight: bold;'>Code</span> : {CODE_AGENC} </p>"+"<p> <span style='font-weight: bold;'>Nom</span> : {NOM_AGENCE} </p>"+"<p><span style='font-weight: bold;'>Ville</span> : {VILLE_COMM} </p>"+"<p> <span style='font-weight: bold;'>Adresse</span> : {ADRESSE} </p>"+"<p> <span style='font-weight: bold;'>Télephone</span> : {TEL_DCA} </p>"
//           });
//       let marocRenderer = {
//           type: "simple", // autocasts as new SimpleRenderer()
//           symbol: {
//           type: "simple-fill", // autocasts as new SimpleFillSymbol()
//           style: "solid",
//           outline: { // autocasts as new SimpleLineSymbol()
//           color: "blue",
//           width: 2
//           },
//             color: [0, 0, 0, 0] // Transparent color
//           }
//         };
//      var highlightSymbol = {
//     type: "simple-marker",
//     color: [255, 255, 0], // Couleur jaune
//     size: 10,
//     outline: {
//       color: [0, 255, 255], // Couleur bleu cyan
//       width: 2
//     }
//     };
//       const maroc = new FeatureLayer({
//         url:"https://services2.arcgis.com/MX1YTHWGW4mL3eoF/arcgis/rest/services/maroc/FeatureServer/0",
//         renderer:marocRenderer,
//       });
//      const postes_client = new FeatureLayer({
//         url:"https://services2.arcgis.com/MX1YTHWGW4mL3eoF/arcgis/rest/services/poste_client/FeatureServer/0",
//         outFiealds: ["CODE_AGENC","NOM_AGENCE","VILLE_COMM","ADRESSE","TEL_DCA"], 
//         popupTemplate: popupClient,
//       });
//     map.add(maroc);
//     map.add(postes_client);
//     view.ui.add(
//             new Expand({
//             view: view,
//             content: new Editor({
//             view: view
//             })
//             }),
//             "top-left"
//           );
// //});

// const sqlExpressions = ["-----choisir la REGION----", "REGION = 'AGADIR'", "REGION = 'CASABLANCA'", "REGION = 'FES'", "REGION = 'MARRAKECH'", "REGION = 'TANGER'", "REGION = 'MEKNES'", "REGION = 'OUJDA'", "REGION = 'RABAT'", "REGION = 'SETTAT'"];
// const selectFilter = document.createElement("select");
// sqlExpressions.forEach(function (sql) {
//     let option = document.createElement("option");
//     option.value = sql;
//     option.innerHTML = sql;
//     selectFilter.appendChild(option);
// });
// function setFeatureLayerFilter1(expression) {
//   postes_client.definitionExpression = expression;
// }
// selectFilter.addEventListener('change', function (event) {
//     setFeatureLayerFilter1(event.target.value);
//     zoomToLayer(postes_client)
// });
// function zoomToLayer(layer) {
//           return layer.queryExtent().then((response) => {
//             view.goTo(response.extent)
//             .catch((error) => {
//               console.error(error);
//             });
//           });
//         }
  //  const filterDiv = document.getElementById("control");
  //  filterDiv.appendChild(selectFilter);
       
//view2.ui.add(selectFilter, "top-right");
  </script>
<style>
#viewDiv {
  width: 80%; /* Définir une largeur de 20% de la largeur de la page */
  background-color: hsl(0, 0%, 100%); /* Exemple : couleur de fond gris clair */
  position: fixed; /* Fixer la div en position absolue par rapport à la fenêtre */
  top: 0; /* Placer la div en haut de la page */
  right: 0; /* Placer la div à droite de la page */
  height: 100%; /* Définir la hauteur de la div pour occuper toute la hauteur de la page */
  /* Exemple : ajouter un padding pour l'espace intérieur */
  box-sizing: border-box; /* Inclure les bordures et le padding dans la largeur de 20% */
  border: 1px solid}
  </style>