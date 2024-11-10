<template>

<div id = container >
<div class="d-grid gap-2" style="margin-top: 20px;">
  <button class="btn btn-outline-primary" type="button" id="editer" >Editer les géométries</button><br>
</div>
<label>filtrer les station de transport par :</label>
<select id="region" class="form-select" aria-label="Default select example"> 
  <option value="" disabled selected> région</option>
</select><br>
<select id="province" class="form-select" aria-label="Default select example"> 
  <option value="" disabled selected> province</option>
</select><br>
<select id="stationtype" class="form-select" aria-label="Default select example"> 
  <option value="" disabled selected>type</option>
  <option value="">vide</option>

</select><br>
 <label for="">Rechercher les stations par:</label>
  <div class="d-grid gap-2">   
      <button class="btn btn-outline-primary" type="button" id="buffer" >Approximité (buffer)</button>
      <button class="btn btn-outline-primary" type="button" id="sketch">Zonne dessinée </button><br>
      <button class="btn btn-outline-primary" type="button" id="dashbord">Dashbord</button><br><br>
      <button class="btn btn-outline-primary" type="button" id="initialiser">Initialiser carte </button>
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
#container {
  padding-top: 50px;
  padding-right :10px; 
  padding-left: 10px;
  position: absolute;
  border: 0px solid rgb(0, 0, 0); 
  width: 20%; 
  position: relative; 
  top: 0; 
  left: 0; 
  height: 100%; 
  box-sizing: border-box; 
}

/* Style pour les étiquettes */
label {
  display: block;
  margin-top:0px;
  margin-bottom: 5px;
  color: #333;
  font-family: "Roboto, Helvetica Neue, sans-serif";
}

/* Style du sélecteur personnalisé */






</style>