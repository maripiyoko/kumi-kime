<template>
  <div id="box-list">
    <div>
      <h2>{{name}}</h2>
      <input class="new-box"
        autofocus autocomplete="off"
        placeholder="新しい役の名前を入力してください"
        v-model="newBox"
        @keyup.enter="addBox" />
    </div>
    <ul class="editable-list">
      <li v-for="box in boxes"
        class="box"
        :key="box.id"
        :class="{ editing: box == editedBox }">
        <div class="view">
          <label @dblclick="editBox(box)">{{box.name}} ({{box.num_requirements}})</label>
          <button class="destroy" @click="removeBox(box)">消す</button>
        </div>
        <input class="edit" type="text"
          v-model="box.name"
          v-box-focus="box == editedBox"
          @blur="doneEdit(box)"
          @keyup.enter="doneEdit(box)"
          @keyup.esc="cancelEdit(box)" />
      </li>
    </ul>
  </div>
</template>

<script>
var STORAGE_KEY = 'boxes-list'
var boxStorage = {
  fetch: function() {
    var boxes = JSON.parse(localStorage.getItem(STORAGE_KEY) || '[]')
    boxes.forEach(function (box, index) {
      box.id = index
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
  data () {
    return {
      name: 'box list default name',
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
        id: boxStorage.uid++,
        name: value,
        num_requirements: 2
      })
      this.newBox = ''
    },
    editBox: function(box) {
      this.beforeEditCache = box.name
      this.editedBox = box
    },
    doneEdit: function(box) {
      if (!this.editedBox) {
        return
      }
      this.editedBox = null
      box.name = box.name.trim()
      if(!box.name) {
        this.removeBox(box)
      }
    },
    cancelEdit: function(box) {
      this.editedBox = null
      box.name = this.beforeEditCache
    },
    removeBox: function(box) {
      this.boxes.slice(this.boxes.indexOf(box), 1)
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
#box-list {
  max-width: 300px;
  border-right: 1px solid #999;
  max-hight: 100%;
  padding: 30px;
}
</style>
