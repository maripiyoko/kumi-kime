<template>
  <div class="mdl-tabs mdl-js-tabs mdl-js-ripple-effect　mdl-cell mdl-cell--6-col">
    <div class="mdl-tabs__tab-bar">
      <a href="#players-load-panel" class="mdl-tabs__tab is-active">参加者の登録</a>
      <a href="#players-match-panel" class="mdl-tabs__tab">結果確認</a>
    </div>

    <div class="mdl-tabs__panel is-active" id="players-load-panel">
      <div id="player-list" class="">
        <h4>1人ずつ登録</h4>
        <div class="mdl-textfield mdl-js-textfield mdl-textfield--floating-label">
          <input class="new-box mdl-textfield__input"
            id="new_player"
            type="text"
            autofocus autocomplete="off"
            placeholder=""
            v-model="newPlayer"
            @keyup.enter="addPlayer" />
          <label class="mdl-textfield__label" for="new_player">新しい参加者の名前</label>
        </div>

        <h4>まとめて登録</h4>
        <div class="mdl-grid">
          <div class="mdl-cell mdl-cell--8-col mdl-textfield mdl-js-textfield mdl-textfield--floating-label">
            <textarea class="mdl-textfield__input"
              v-model="importingPlayersCsv"
              id="players_batch_import"
              type="text" 
              rows= "10"></textarea>
            <label class="mdl-textfield__label" for="players_batch_import">取り込む参加者をコピペで貼り付けてください</label>
          </div>
          <div class="mdl-cell mdl-cell--4-col">
            <button class="mdl-button mdl-js-button mdl-button--raised mdl-js-ripple-effect mdl-button--accent"
              @click="importPlayers()">
              取り込み
            </button>
          </div>
        </div>
        <ul class="mdl-list">
          <li v-for="player in players" class="player mdl-list__item">
            {{player.id}}. {{player.name}} {{player.choices}}
          </li>
        </ul>
      </div>
    </div>

    <div class="mdl-tabs__panel" id="players-match-panel">
      <h3>結果を確認する</h3>
      <div>
        <button class="mdl-button mdl-js-button mdl-button--raised mdl-js-ripple-effect mdl-button--accent">
          組み合わせを探す
        </button>
        <ul class="mdl-list">
          <li v-for="player in players" class="player mdl-list__item">
            {{player.id}}. {{player.name}} {{player.choices}}
          </li>
        </ul>
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
    addPlayer: function() {
      var value = this.newPlayer && this.newPlayer.trim()
      if (!value) {
        return
      }
      this.players.push({
        id: playerStorage.uid++,
        name: value,
        choices: []
      })
      this.newPlayer = ''
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
          })
        })
      })
      if (resultPlayers.length > 0) {
        this.players = resultPlayers
      }
    }
  }
}
</script>
