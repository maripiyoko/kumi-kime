<template>
  <div>
    <div class="mdl-layout__tab-panel" id="players-load-panel">
      <div id="player-list" class="mdl-grid">
        <div class="mdl-cell mdl-cell--6-col">
          <h4>1人ずつ登録</h4>
          <div class="mdl-cell mdl-cell--6-col mdl-textfield mdl-js-textfield mdl-textfield--floating-label">
            <input class="new-box mdl-textfield__input"
              id="new_player"
              type="text"
              autofocus autocomplete="off"
              placeholder=""
              v-model="newPlayer"
              @keyup.enter="addPlayer" />
            <label class="mdl-textfield__label" for="new_player">新しい参加者の名前</label>
          </div>

          <ul class="mdl-list">
            <li v-for="player in players" class="player mdl-list__item">
              {{player.id}}. {{player.name}} {{player.choices}} : {{player.boxId}}
            </li>
          </ul>
        </div>

        <div class="mdl-cell mdl-cell--6-col">
          <h4>まとめて登録</h4>
          <div class="mdl-textfield mdl-js-textfield mdl-textfield--floating-label">
            <textarea class="mdl-textfield__input"
              v-model="importingPlayersCsv"
              id="players_batch_import"
              type="text" 
              rows= "10"></textarea>
            <label class="mdl-textfield__label" for="players_batch_import">取り込む参加者をコピペで貼り付けてください</label>
          </div>
          <div class="">
            <button class="mdl-button mdl-js-button mdl-button--raised mdl-js-ripple-effect mdl-button--accent"
              @click="importPlayers()">
              取り込み
            </button>
            <button class="mdl-button mdl-js-button mdl-button--raised mdl-js-ripple-effect" 
              @click="clearAllPlayers()">
              参加者を全部クリアする
            </button>
          </div>
        </div>
      </div>
    </div>

    <div class="mdl-layout__tab-panel mdl-grid" id="players-match-panel">
      <div class="mdl-cell mdl-cell--12-col">
        <button class="mdl-button mdl-js-button mdl-button--raised mdl-js-ripple-effect mdl-button--accent"
          @click="matchBoxPlayers()">
          組み合わせを探す
        </button>
      </div>
      <div>
        <div class="mdl-grid">
          <div v-for="boxPlayer in boxPlayers" :key="boxPlayer.id" 
            class="box-player-card mdl-card mdl-cell mdl-cell--4-col">
            <div class="mdl-card__title mdl-card--expand">
              <ul>
                <li v-for="player in boxPlayer.players">
                  {{player.name}} {{player.choices}}
                </li>
              </ul>
            </div>
            <div class="mdl-card__actions mdl-card--border">
              {{boxPlayer.box.id}}. {{boxPlayer.box.name}} ({{boxPlayer.box.num_requirements}})
            </div>
          </div>
        </div>
      </div>
    </div>
  </div>
</template>

<script>
var STORAGE_KEY = 'players-list'
var playerStorage = {
  fetch: function() {
    var players = JSON.parse(localStorage.getItem(STORAGE_KEY) || '[]')
    players.forEach(function (player, index) {
      player.id = index
    })
    playerStorage.uid = players.length
    return players
  },
  save: function(players) {
    localStorage.setItem(STORAGE_KEY, JSON.stringify(players))
  }
}
export default {
  name: 'player-list',
  props: ['boxPlayers'],
  data () {
    return {
      name: '参加者一覧',
      newPlayer: '',
      importingPlayersCsv: '',
      players: playerStorage.fetch(),
      editedPlayer: null
    }
  },
  watch: {
    players: {
      handler: function(players) {
        playerStorage.save(players)
      },
      deep: true
    }
  },
  methods: {
    isNoPlayer: function() {
      return this.players.length == 0
    },
    addPlayer: function() {
      var value = this.newPlayer && this.newPlayer.trim()
      if (!value) {
        return
      }
      this.players.push({
        id: playerStorage.uid++,
        name: value,
        choices: [],
        boxId: ''
      })
      this.newPlayer = ''
    },
    clearAllPlayers: function() {
      this.players = []
    },
    importPlayers: function() {
      var value = this.importingPlayersCsv && this.importingPlayersCsv.trim()
      if (!value) {
        return
      }
      var lines = value.split("\n")
      var resultPlayers = []
      lines.forEach(function(line, index) {
        var cells = line.split(",")
        resultPlayers.push({
          id: index,
          name: cells[0].trim(),
          choices: cells.slice(1).map(function(n) {
            return parseInt(n)
          }),
          boxId: ''
        })
      })
      if (resultPlayers.length > 0) {
        this.players = resultPlayers
      }
    },
    matchBoxPlayers: function() {
      this.$emit('matchBoxPlayers', this.players);
    }
  }
}
</script>

<style>
.box-player-card.mdl-card {
  width: 320px;
  height: 320px;
}
.box-player-card > .mdl-card__title {
  color: #fff;
}
.box-player-card > .mdl-card__actions {
  background-color: #fff;
  color: #222;
}
</style>
