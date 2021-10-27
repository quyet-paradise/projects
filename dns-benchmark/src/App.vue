<template>
  <div id="app">
    <div id="nav">
      <img alt="Paradise logo" class="logo-img" src="/Paradiselogo.png">
    </div>
    <MainContent />
  </div>
</template>

<script>
import MainContent from './components/MainContent.vue'

export default {
  name: 'App',
  components: {
    MainContent
  },
  methods: {
    
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
}
.logo-img {
  width: 150px;
  height: auto;
  display: block;
  margin-left: 20px;
}
</style>
