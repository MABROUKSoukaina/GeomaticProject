<template>
  <div id="queryDiv" class="esri-widget">
      <b>recherche par buffer</b><br />
      <br />choisir une géométrie:
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
      <div>
        Entrer une valeur de buffer:
        <div id="bufferNum"></div>
      </div>
      <br />
      <button class="esri-button" id="clearGeometry" type="button" style="padding-right: 10px;">supprimer</button>
    </div>

    <div id="resultDiv" class="esri-widget">
  <div class="count">
    Stations sélectionnées:
    <div class="count" id="count">0</div>
  </div>
  <div class="charts">
    <div>
     <canvas id="material-chart" width="360" height="360"></canvas>
    </div>
     <!-- <div>
      <canvas id="year-chart" height="350" width="260"></canvas>
    </div> -->
  </div>
</div>

</template>
<script>
 export default {
    methods: {

        
   }
 };
</script> 
<style>
.esri-ui-top-right {
        max-height: 100%;
      }

      #queryDiv
      
      {
        width: 250px;
        font-size: 14px;
        padding: 10px;
        display: block;
        overflow-y: auto;
        overflow-x: hidden;
      }

      #resultDiv
      {
        width: 350px;
        font-size: 14px;
        padding: 10px;
        display: block;
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
        background: #000;
        padding-right: 10px;
      }
      .clearGeometry{
        padding-right: 10px;
      }
      .count {
        white-space: nowrap;
        font-size: 14px;
        font-weight: bold;
        display: inline-block;
      }
</style>