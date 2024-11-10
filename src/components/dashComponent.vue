<template>
 <div id="resultDiv" class="esri-widget">
  <div class="count">
    Stations sélectionnées:
    <div class="count" id="count">0</div>
  </div>
  <div class="charts">
    <div>
     <canvas id="material-chart" width="250" height="300"></canvas>
    </div>
     <div>
      <canvas id="year-chart" height="250" width="260"></canvas>
    </div>
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
 #resultDiv {
        min-width: 250px;
        font-size: 14px;
        padding: 10px;
        overflow-y: auto;
        overflow-x: hidden;
      }
 .count {
        white-space: nowrap;
        font-size: 14px;
        font-weight: bold;
        display: inline-block;
      }
</style>