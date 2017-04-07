<template>
  <div id="app">
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
  hight: 100%;
}
.editable-list {
  padding: 0;
}
.editable-list li {
  position: relative;
  list-style: none;
}
.editable-list .view {
  background-color: yellow;
  margin: 20px 0;
  z-index: 10;
}
.editable-list .edit {
  position: absolute;
  top: 0;
}
input {
  width: 100%;
  font-size: 18px;
  padding: 5px;
}
</style>
