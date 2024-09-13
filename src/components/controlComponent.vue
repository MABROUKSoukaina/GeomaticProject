<template>
  <div id = container >
  <div id="control"></div> <hr>
  <div id="client"></div><hr>
  <div class="d-grid gap-2">
      <button class="btn btn-primary" type="button" >Effectuer la visite</button> <br>
      <button class="btn btn-success" type="button" id="demarrer">Démarrer</button> <br>
      <button class="btn btn-warning" type="button" id="initialiser" >Initialiser</button>
  </div>
  </div> 
    <div class="modal fade" id="myModal" tabindex="-1" aria-labelledby="exampleModalLabel" aria-hidden="true">
    <div class="modal-dialog">
      <div class="modal-content">
        <div class="modal-header">
          <h1 class="modal-title fs-5" id="exampleModalLabel">Alerte!</h1>
          <button type="button" class="btn-close" data-bs-dismiss="modal" aria-label="Close"></button>
        </div>
        <div class="modal-body">
          Veuillez choisir un point de départ !
        </div>
        <div class="modal-footer">
          <button type="button" class="btn btn-secondary" data-bs-dismiss="modal">Fermer</button>
        </div>
      </div>
    </div>
  </div>
 <div class="modal fade" id="terminer_pop" tabindex="-1" aria-labelledby="exampleModalLabel" aria-hidden="true">
      <div class="modal-dialog">
        <div class="modal-content">
          <div class="modal-header">
            <h1 class="modal-title fs-5" id="exampleModalLabel">Massage!</h1>
            <button type="button" class="btn-close" data-bs-dismiss="modal" aria-label="Close"></button>
          </div>
          <div class="modal-body">
            <b> Bravo! vous avez terminer votre tournée pour cette région.</b>
          </div>
          <div class="modal-footer">
            <button type="button" class="btn btn-secondary" data-bs-dismiss="modal">Fermer</button>
          </div>
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
#container {
  position: relative;
  border: 1px solid rgb(0, 0, 0); 
  width: 20%; /* Définir une largeur de 18% de la largeur de la page */
  background-color: hsl(40, 67%, 96%);/* Exemple : couleur de fond gris clair */
  position: fixed; /* Fixer la div en position absolue par rapport à la fenêtre */
  top: 0; /* Placer la div en haut de la page */
  left: 0; /* Placer la div à droite de la page */
  height: 100%; /* Définir la hauteur de la div pour occuper toute la hauteur de la page */
  padding: 2px; /* Exemple : ajouter un padding pour l'espace intérieur */
  box-sizing: border-box; /* Inclure les bordures et le padding dans la largeur de 20% */
  border: 1px solid;
  }
  #client{
    position: relative;
    display: block;
    /* background-color: #fff; */
    width: 100%; 
    height: 9%;
    display: flex;
    align-items: center;
    justify-content: center; 
  }
  #control {
    display: block;
    width: 100%; 
    height: 9%; 
    position: relative;
    display: flex;
    align-items: center;
    justify-content: center;
  }
  .demmarrer {
    display: relative;
    width: 100%; 
    height: 9%; 
    position: relative;
    display: flex;
    align-items: center;
    justify-content: center;
  }
  #add{
   
    display: relative;
    width: 100%; 
    height: 9%; 
    position: relative;
    display: flex;
    align-items: center;
    justify-content: center;
  }
  #init{
    display: relative;
    width: 100%; 
    height: 10%; 
    position: relative;
    display: flex;
    align-items: center;
    justify-content: center;
    
  }

.custom-dropdown {
  position: relative;
  width: 225px; /* Définissez la largeur du menu déroulant ici */
  border: 1px solid #ccc;
  border-radius: 4px;
  overflow: hidden;
}
/* Styles pour le sélecteur d'options personnalisé */
.custom-dropdown select {
  width: 100%;
  padding: 10px;
  border: none;
  outline: none;
  background-color: transparent;
  font-family: Arial, sans-serif;
  font-size: 16px;
  color: #333;
}

.custom-dropdown option {
  font-family: Arial, sans-serif;
  font-size: 16px;
  
}
.popup {
  display: none;
  position: fixed;
  top: 50%;
  left: 50%;
  transform: translate(-50%, -50%);
  padding: 20px;
  background-color: white;
  border: 1px solid #ccc;
  box-shadow: 0 2px 5px rgba(0, 0, 0, 0.3);
  z-index: 1000;
}
.popup p {
  margin: 0;
  text-align: center;
}
</style>