<template>
  <div id="box-list" class="mdl-cell mdl-cell--6-col">
    <div>
      <h3>役の登録</h3>
      <div class="mdl-textfield mdl-js-textfield mdl-js-textfield mdl-textfield--floating-label">
        <input class="new-box mdl-textfield__input"
          id="new_box"
          type="text"
          autofocus autocomplete="off"
          placeholder=""
          v-model="newBox"
          @keyup.enter="addBox" />
        <label class="mdl-textfield__label" for="new_box">新しい役の名前</label>
      </div>
      <span>({{boxes.length}})</span>
    </div>
    <div>
      <table class="mdl-data-table mdl-js-data-table mdl-shadow--2dp" v-show="boxes.length">
        <thead>
          <th>ID</th>
          <th class="mdl-data-table__cell--non-numeric">役の名前</th>
          <th>数</th>
          <th></th>
        </thead>
        <tbody>
          <tr v-for="box in boxes" class="box" :key="box.id">
            <td>{{box.id}}</td>
            <td class="mdl-data-table__cell--non-numeric">
              <input class="mdl-textfield__input" type="text" v-model="box.name"/>
            </td>
            <td>
              <input class="mdl-textfield__input num-input" type="text" pattern="[0-9]*"
                v-model="box.num_requirements" />
            </td>
            <td>
              <button class="mdl-button mdl-js-button mdl-button--icon"
                @click="removeBox(box)">
                <i class="material-icons">remove</i>
              </button>
            </td>
          </tr>
        </tbody>
      </table>
    </div>
    <div>
      <ul class="mdl-list" v-show="remainingPlayers">
        <li v-for="player in remainingPlayers" class="player mdl-list__item">
          {{player}}
        </li>
      </ul>
    </div>
    <hr/>
    <button class="mdl-button mdl-js-button mdl-button--raised mdl-js-ripple-effect" @click="clearAllBoxes()">
      役を全部クリアする
    </button>
  </div>
</template>

<script>
var STORAGE_KEY = 'boxes-list'
var boxStorage = {
  fetch: function() {
    var boxes = JSON.parse(localStorage.getItem(STORAGE_KEY) || '[]')
    boxes.forEach(function (box, index) {
      if (!box.id) {
        box.id = index + 1
      }
    })
    boxStorage.uid = boxes.length
    return boxes
  },
  save: function(boxes) {
    localStorage.setItem(STORAGE_KEY, JSON.stringify(boxes))
  }
}

export default {
  name: 'box-list',
  props: ['remainingPlayers'],
  data () {
    return {
      newBox: '',
      boxes: boxStorage.fetch(),
      editedBox: null
    }
  },
  watch: {
    boxes: {
      handler: function(boxes) {
        boxStorage.save(boxes)
      },
      deep: true
    }
  },
  methods: {
    addBox: function() {
      var value = this.newBox && this.newBox.trim()
      if (!value) {
        return
      }
      this.boxes.push({
        id: ++boxStorage.uid,
        name: value,
        num_requirements: 2
      })
      this.newBox = ''
    },
    editBoxName: function(box) {
      alert('editBoxName')
      this.beforeEditCache = box.name
      this.editedBox = box
    },
    editBoxNum: function(box) {
      this.beforeEditCache = box.num_requirements
      this.editedBox = box
    },
    doneNameEdit: function(box) {
      if (!this.editedBox) {
        return
      }
      this.editedBox = null
      box.name = box.name.trim()
      if(!box.name) {
        this.removeBox(box)
      }
    },
    doneNumEdit: function(box) {
      if (!this.editedBox) {
        return
      }
      this.editedBox = null
      if(!box.num_requirements) {
        this.removeBox(box)
      }
    },
    cancelNameEdit: function(box) {
      this.editedBox = null
      box.name = this.beforeEditCache
    },
    cancelNumEdit: function(box) {
      this.editedBox = null
      box.num_requirements = this.beforeEditCache
    },
    removeBox: function(box) {
      this.boxes.splice(this.boxes.indexOf(box), 1)
    },
    clearAllBoxes: function() {
      this.boxes = []
      boxStorage.uid = 0
    }
  },

  directives: {
    'box-focus': function (el, value) {
      if (value) {
        el.focus()
      }
    }
  }
}
</script>

<style>
</style>
