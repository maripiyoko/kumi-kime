<template>
  <div id="app" class="mdl-grid">
    <box-list></box-list>
  </div>
</template>

<script>
import BoxList from './components/BoxList'

export default {
  name: 'app',
  components: {
    BoxList
  }
}
</script>

<style>
#app {
  font-family: 'Avenir', Helvetica, Arial, sans-serif;
  -webkit-font-smoothing: antialiased;
  -moz-osx-font-smoothing: grayscale;
  color: #2c3e50;
  margin: 0;
}
.num-input {
  width: 40px;
  text-align: right;
}
</style>
