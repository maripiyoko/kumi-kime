<template>
  <div id="app" class="mdl-grid">
    <box-list ref="boxref"></box-list>
    <player-list v-on:matchBoxPlayers="players_callback"></player-list>
  </div>
</template>

<script>
import BoxList from './components/BoxList'
import PlayerList from './components/PlayerList'

export default {
  name: 'app',
  components: {
    BoxList, PlayerList
  },
  methods: {
    players_callback: function(players) {
      console.log(players)
      var boxes = this.$refs.boxref.boxes
      console.log(boxes)
    },
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
