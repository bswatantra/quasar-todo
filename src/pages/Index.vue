/* eslint-disable no-mixed-spaces-and-tabs */
/* eslint-disable no-tabs */
<template>
<q-page padding>
<div class="q-pb-md">
<form name="todo_form" @submit.prevent="add_todo" id="todoForm">
<q-input class="q-pt-md" name="title" outlined v-model="title" label="Title" />
<q-input class="q-pt-md q-pb-md" name="content" outlined v-model="content" label="Content" />
<q-btn color="primary" type="submit" label="Submit" />
<p v-for="error in errors" :key="error" color="text-primary">
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
<div class="q-pa-md scroll" style="height: 300px">
      <div  v-for="(post, index) in posts" :key="index" class="q-mb-sm">
        <q-badge color="secondary">
           {{ posts.length - index }}
        </q-badge>
        {{ post.title }}
        <!-- {{ post.content }} -->
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
      posts: [],
      errors: [],
      title: '',
      content: ''
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
          this.posts = response.data
        // console.log(response.data)
        })
        .catch(error => {
          this.loading = false
          console.log(error)
        })
    }
  }
}
</script>
