<template>
  <div id="app" class="mdl-layout mdl-js-layout mdl-layout--fixed-header mdl-layout--fixed-tabs">
    <header class="mdl-layout__header">
      <div class="mdl-layout__header-row">
        <span class="mdl-layout-title">組み合わせを決めよう</span>
      </div>
      <div class="mdl-layout__tab-bar mdl-js-ripple-effect">
        <a href="#scroll-tab-1" class="mdl-layout__tab js-active">役 (Box)</a>
        <a href="#players-load-panel" class="mdl-layout__tab">参加者 (Player)</a>
        <a href="#players-match-panel" class="mdl-layout__tab">組み合わせ結果</a>
      </div>
    </header>
    
    <main class="mdl-layout__content">
      <section class="mdl-layout__tab-panel is-active mdl-grid" id="scroll-tab-1">
        <box-list ref="boxref" :boxPlayers="boxPlayers" :remainingPlayers="remainingPlayers"></box-list>
      </section>
      <player-list v-on:matchBoxPlayers="players_callback" :boxPlayers="boxPlayers"　:remainingPlayers="remainingPlayers"></player-list>
    </main>

    <footer class="mdl-mini-footer">
      <div class="mdl-mini-footer__left-section">
        <ul class="mdl-mini-footer__link-list">
          <li><a href="http://kumikime.zuccha.net">KumiKime Help</a></li>
        </ul>
      </div>
    </footer>
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
          return player.choices.toString().indexOf(String(b.id)) >= 0
        })
        boxPlayers.push({
          id: b.id,
          box: b,
          players: filteredPlayers
        })
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
    }
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
.box-player-card.mdl-card {
  width: 256px;
  height: 256px;
  background: #3E4EB8;
}
</style>
