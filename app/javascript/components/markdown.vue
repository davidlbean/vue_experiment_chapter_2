<template>
  <div>
    <section class='main'>
      <textarea v-model="content"></textarea>
    </section>

    <aside class='preview' v-html="notePreview">
    </aside>
  </div>
</template>

<script>

import marked from 'marked';

export default {
  data() {
    return {
      content: localStorage.getItem('content') || 'this is a note'
    }
  },

  computed: {
    notePreview() {
      return marked(this.content)
    }
  },

  methods: {
    saveNote() {
      console.log('saving note:', this.content)
      localStorage.setItem('content', this.content)
    },
  },

  watch: {
    content: 'saveNote'
  }
}


</script>

<style scoped>
</style>