<template>
<q-page padding>
<div class="q-pb-md">
<q-input class="q-pt-md" outlined v-model="title" label="Title" />
<q-input class="q-pt-md q-pb-md" outlined v-model="content" label="Content" />
<q-btn color="primary" label="Submit" />
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
        {{ post.data.title }}
      </div>
  </div>
  </q-page>
</template>
<script>
import axios from 'axios'
export default {
  name: 'PageIndex',
  created () {
    axios.get('https://www.reddit.com/r/aww.json?raw_json=1')
      .then(response => {
        this.loading = false
        this.posts = response.data.data.children
      })
      .catch(error => {
        this.loading = false
        console.log(error)
      })
  },
  data () {
    return {
      showImageDialog: false,
      imageUrl: '',
      loading: true,
      posts: []
    }
  },
  methods: {
    showImage (image) {
      this.showImageDialog = true
      this.imageUrl = image
    }
  }
}
</script>
