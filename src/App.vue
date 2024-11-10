
<template>
  <div>
  <controlComponent/>
  <carteComponent/>
  <bufferComponent/>
  </div>
</template>

<script>
import carteComponent from './components/carteComponent.vue';
import controlComponent from './components/controlComponent.vue';
import bufferComponent from './components/bufferComponent.vue';
import dashComponent from './components/dashComponent.vue';
export default {
  components: {
    carteComponent, controlComponent, bufferComponent, dashComponent

  }
};
</script>

<style>
html, body, #app {
   background: "white";
   background: linear-gradient(90deg, rgb(245, 251, 252), rgb(236, 246, 252));
  height: 100%;
  margin: 0;
  padding: 0;
}
</style>
