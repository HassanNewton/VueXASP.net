<template>
  <!-- Anropa ProductList-komponenten här -->
  <ProductList></ProductList>
</template>

<script>
import ProductList from "./components/ProductList.vue"; // Importera ProductList

export default {
  name: "App",
  components: {
    ProductList, // Registrera den här komponenten
  },
};
</script>

<style>
#app {
  display: flex;
  padding-bottom: 30%;
  font-family: Avenir, Helvetica, Arial, sans-serif;
  -webkit-font-smoothing: antialiased;
  -moz-osx-font-smoothing: grayscale;
  text-align: center;
  color: #000000;
  background-color: blanchedalmond;
  margin-top: 60px;
}
body {
  background-color: blanchedalmond;
}
</style>
