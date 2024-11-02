<template>
  <div id = container >
  <div id="region_div">
  <label for="region"> Filtrer par région :</label>
  <select id="region" class="custom-select">
    <option value="default">choisissez la région</option>
  </select>
</div>

<div id="province_div">
  <label for="province"> Filtrer par province :</label>
  <select id="province" class="custom-select">
    <option value="default">choisissez la province</option>
  </select>
</div>

<div id="station_div">
  <label for="station"> filtrer par le type de station :</label>
  <select id="stationtype" class="custom-select">
    <option value="default">choisissez le type de la station</option>
  </select>
</div>
<br>

  <div class="d-grid gap-2">
      <button class="btn btn-outline-primary" type="button" style="background-color: white; color: #000;" id="buffer" >Recherche par approximité</button><br>
      <button class="btn btn-outline-primary" type="button" style="background-color: white; color: #000; margin-bottom: 100px;" id="sketch">Dessiner une zone de recherche</button> 
      <button class="btn btn-outline-primary" type="button" id="initialiser" style="background-color: white; color: #000;" >Initialiser</button>
  </div>
  </div>

  <!-- Div pour la recherche avec buffer -->

  <div id="queryDiv" class="esri-widget">
      <b></b><br />
      <br />dessiner une géomertie avec:  
      <div class="geometry-options">
        <button
          class="esri-widget--button esri-icon-map-pin geometry-button"
          id="point-geometry-button"
          value="point"
          title="Query by point"
        ></button>
        <button
          class="esri-widget--button esri-icon-polyline geometry-button"
          id="line-geometry-button"
          value="polyline"
          title="Query by line"
        ></button>
        <button
          class="esri-widget--button esri-icon-polygon geometry-button"
          id="polygon-geometry-button"
          value="polygon"
          title="Query by polygon"
        ></button>
      </div>
      <br />
      <div class="tooltip">
        <label for="bufferNum">entrer la valeur de Buffer</label>
        <div id="bufferNum"></div>
      </div>
      <br />
      <button class="esri-button" id="clearGeometry" type="button">Effacer</button>
    </div>
  
</template>
 <script>
 export default {
   methods: {
   }
 };
</script> 
<style>
#container {
  
  padding: 10px;
  position: absolute;
  border: 0px solid rgb(0, 0, 0); 
  width: 20%; 
  position: relative; 
  top: 0; 
  left: 0; 
  height: 100%; 
  box-sizing: border-box; 
}

 
#region_div, #province_div,#station_div {
  margin-bottom: 20px;
  font-family: 'Roboto, sans-serif'
}

/* Style pour les étiquettes */
label {
  display: block;
  margin-top:8px;
  margin-bottom: 8px;
  color: #333;
  font-family: "Roboto, Helvetica Neue, sans-serif";
}

/* Style du sélecteur personnalisé */
.custom-select {
  width: 100%;
  padding: 10px;
  border: 1px solid #1147fa;
  border-radius: 8px;
  background-color: #f9f9f9;
  font-size: 16px;
  color: #333;
  appearance: none;
  background-image: url("data:image/svg+xml,%3Csvg xmlns='http://www.w3.org/2000/svg' viewBox='0 0 20 20'%3E%3Cpath fill='%23333' d='M5.516 7.093a.734.734 0 011.034 0L10 10.544l3.45-3.451a.734.734 0 111.034 1.034L10.5 12.612a.734.734 0 01-1.034 0L5.516 8.127a.734.734 0 010-1.034z'/%3E%3C/svg%3E");
  background-repeat: no-repeat;
  background-position: right 10px center;
  background-size: 12px;
  cursor: pointer;
}

/* Effet au survol */
.custom-select:hover {
  border-color: #999;
}

/* Effet au focus */
.custom-select:focus {
  border-color: #666;
  outline: none;
}

/* Style pour la liste déroulante des options */
.custom-select option {
  padding: 10px;
  background-color: #ffffff;
  color: #333;
  font-size: 16px;
}
#queryDiv{
        min-width: 250px;
        font-size: 14px;
        padding: 10px;
        display: none;
        overflow-y: auto;
        overflow-x: hidden;
      }
 .geometry-options {
        display: flex;
        flex-direction: row;
      }
      .geometry-button {
        flex: 1;
        border-style: solid;
        border-width: 1px;
        border-image: none;
      }
      .geometry-button-selected {
        background: #4c4c4c;
        color: #fff;
      }

      #bufferNum {
        width: 90%;
        margin: 2.5em auto 0;
      }

      .count {
        white-space: nowrap;
        font-size: 14px;
        font-weight: bold;
        display: inline-block;
      }

</style>