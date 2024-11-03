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
      <div class="tooltip">
        <label for="bufferNum">Set a geometry buffer size:</label>
        <div id="bufferNum"></div>
      </div>
      <br />
      <button class="esri-button" id="clearGeometry" type="button">Clear</button>
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
</style>