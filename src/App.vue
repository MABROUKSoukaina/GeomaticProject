
<template>
  <div>
  <controlComponent/>
  <carteComponent/>
  </div>
</template>

<script>
import carteComponent from './components/carteComponent.vue';
import controlComponent from './components/controlComponent.vue';

export default {
  components: {
    carteComponent, controlComponent

  }
};
</script>

<style>
html, body, #app {
  background: linear-gradient(90deg, rgb(218, 249, 252), rgb(236, 246, 252));
  height: 100%;
  margin: 0;
  padding: 0;
}
</style>
