<template>
  <div id="app" class="mdl-grid">
    <box-list ref="boxref" :boxPlayers="boxPlayers" :remainingPlayers="remainingPlayers"></box-list>
    <player-list v-on:matchBoxPlayers="players_callback" :boxPlayers="boxPlayers"></player-list>
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
  data () {
    return {
      boxPlayers: [],
      remainingPlayers: []
    }
  },
  methods: {
    players_callback: function(players) {
      var boxes = this.$refs.boxref.boxes
      var copiedBoxes = boxes.slice()
      var boxPlayers = []
      copiedBoxes.forEach(function(b) {
        var filteredPlayers = players.filter(function(player) {
          return player.choices.indexOf(b.id) >= 0
        })
        boxPlayers[b.id] = {
          box: b,
          players: filteredPlayers
        } 
      })
      
      var sortedBoxPlayers = boxPlayers.slice()
      sortedBoxPlayers.sort(function(a, b) {
        var aDiff = a.players.length - a.box.num_requirements
        var bDiff = b.players.length - b.box.num_requirements
        return aDiff - bDiff || b.box.num_requirements - a.box.num_requirements
      })
      var chosenPlayers = []
      sortedBoxPlayers.forEach(function(bp) {
        var selectablePlayers = _.difference(bp.players, chosenPlayers)
        selectablePlayers.sort(function(a, b) {
          return a.choices.indexOf(bp.box.id) > b.choices.indexOf(bp.box.id)
        })
        bp.players = selectablePlayers.slice(0, bp.box.num_requirements)
        chosenPlayers = chosenPlayers.concat(bp.players)
        bp.players.forEach(function(e) {
          e.boxId = bp.box.id
        })
      })
      
      // display result
      sortedBoxPlayers.forEach(function(bp) {
        console.log(bp.box.id + '. ' + bp.box.name + '(' + bp.box.num_requirements + ')')
        bp.players.forEach(function(p) {
          console.log("\t" + p.name + ' : ' + p.choices.join(','))
        })
      })
      this.remainingPlayers = _.difference(players, chosenPlayers)
      this.remainingPlayers.forEach(function(p) {
        console.log("\t" + p.name + ' : ' + p.choices.join(','))
      })

      this.boxPlayers = boxPlayers
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
