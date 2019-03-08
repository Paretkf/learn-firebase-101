<template>
  <div class="home">
    <h1>TODOS</h1>
    <ol>
      <li v-for="(todo, index) in todos" :key="index">

        <span v-if="index !== selectedTodo">
          {{todo.name}}
        </span>
  
        <input type="text" v-model="todo.name"
              @input="update(index, todo.name)"
              v-else>
          
        <button @click="selectedTodo = index"
                  v-if="index !== selectedTodo">update</button>

        <button @click="selectedTodo = ''" v-else>cancel</button>

        <button @click="remove(index)">x</button>
      </li>
    </ol>
    <hr>
    <h2>เพิ่ม TODO</h2>
    <input type="text"
            v-on:keydown.enter="addNewTodo()"
            v-model="newTodo">
  </div>
</template>

<script>
import firebase from 'firebase/app'
import 'firebase/database'
import config from '@/config/firebase.js'
firebase.initializeApp(config)

const db = firebase.database()

export default {
  data () {
    return {
      todos: {},
      newTodo: '',
      count: 0,
      selectedTodo: ''
    }
  },
  methods: {
    remove (index) {
      db.ref('todos/' + index).remove()
    },
    update (index, name) {
      db.ref('todos/' + index + '/name').set(name)
      // db.ref('todos/' + index).set({
      //   name: name,
      //   isDone: false
      // })
    },
    addNewTodo () {
      db.ref('todos').push({
        name: this.newTodo,
        isDone: false
      })
      // db.ref('todos/' + this.count++).set({
      //   name: this.newTodo,
      //   isDone: false
      // })
      this.newTodo = ''
    }
  },
  created () {
    db.ref('todos').on('value', val => {
      this.todos = val.val()
    })
  }
}
</script>
