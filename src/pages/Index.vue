<template>
<q-page padding>
<div class="q-pb-md">
<form name="todo_form" @submit.prevent="add_todo()" id="todoForm">
<q-input class="q-pt-md title" name="title" outlined v-model="title" label="Title" />
<q-input class="q-pt-md q-pb-md content" name="content" outlined v-model="content" label="Content" />
<q-btn  v-if="isValid" color="primary" type="submit" label="Submit" />
<p v-for="error in errors" :key="error" class="text-red-10 q-pt-md">
{{ error }}
</p>
</form>
<q-spinner class="q-pt-md"
      v-if="loading"
      color="primary"
      size="3em"
    />
</div>
<h4>Todo List</h4>
<div class="q-pa-md scroll" style="height: 550px">
      <div  v-for="(todo, index) in todos" :key="index" class="q-mb-sm">
        <q-badge color="secondary">
           {{ todos.length - index }}
        </q-badge>
        {{ todo.title }}
        <p>{{todo.content}}</p>
<div>
<q-btn class="q-ml-md" color="red" label="Delete" @click="deleteTodo(todo)" />
<q-btn class="q-ml-md" color="primary" label="Edit" @click="editTodo(todo)" />
</div>
</div>
  </div>
  </q-page>
</template>
<script>
import axios from 'axios'
export default {
  name: 'PageIndex',
  created () {
    this.getAllTodos()
  },
  data () {
    return {
      loading: true,
      todos: [],
      errors: [],
      title: '',
      content: '',
      button: 'Submit'
    }
  },
  methods: {
    add_todo () {
      const formData = new FormData(document.getElementById('todoForm'))
      axios.post('http://todos.test/api/todo', formData)
        .then(response => {
          this.getAllTodos()
        })
        .catch(error => {
          if (error.response.status === 422) {
            this.errors = error.response.data.errors
          }
        })
    },
    getAllTodos () {
      axios.get('http://todos.test/api/todo')
        .then(response => {
          this.loading = false
          this.todos = response.data
        // console.log(response.data)
        })
        .catch(error => {
          this.loading = false
          console.log(error)
        })
    },
    deleteTodo (todo) {
      console.log(todo.id)
      axios.delete(`http://todos.test/api/todo/${todo.id}`)
        .then(response => {
          console.log(response)
          if (response.data === 'ok') {
            const index = this.todos.findIndex(item => item.id === todo.id)
            this.todos.splice(index, 1)
          }
        })
        .catch(error => {
          this.loading = false
          console.log(error)
        })
    },
    editTodo (todo) {
      this.title = todo.title
      this.content = todo.content
    },
    updateTodo (todo) {
      console.log(todo.id)
      axios.patch(`http://todos.test/api/todo/${todo.id}`)
        .then(response => {
          console.log(response)
          if (response.data === 'ok') {
            const index = this.todos.findIndex(item => item.id === todo.id)
            this.todos.splice(index, 1)
          }
        })
        .catch(error => {
          this.loading = false
          console.log(error)
        })
    }
  },
  computed: {
    isValid () {
      console.log(this.title)
      return this.title !== '' && this.content !== ''
    }
  }
}
</script>
