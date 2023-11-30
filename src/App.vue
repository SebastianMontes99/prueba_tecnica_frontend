<template>
  <div id="app">
    <Empresas />
  </div>
</template>

<script>
import Empresas from "@/components/Empresas.vue";

export default {
  name: 'App',
  components: {
    Empresas
  }
}
</script>

<style>
#app {
  font-family: Avenir, Helvetica, Arial, sans-serif;
  -webkit-font-smoothing: antialiased;
  -moz-osx-font-smoothing: grayscale;
  text-align: center;
  color: #2c3e50;
  margin-top: 60px;
}
</style>
