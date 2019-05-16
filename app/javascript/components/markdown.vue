<template>
  <div id='notebook'>
    <aside class='side-bar'>
      <div class='toolbar'>
        <button v-on:click='addNote' :title='addButtonTitle'><i class='material-icons'>add</i> Add Note</button>
      </div>
      <div class='notes'>
        <div 
          v-for="note in sortedNotes" 
          v-on:click='selectNote(note)'
          :class="{selected: note === selectedNote}"
          class='note'>
            {{ note.title }}
            <i class='icon material-icons' v-if="note.favorite">star</i>
          </div>
      </div>
    </aside>

    <template v-if='selectedNote'>
      <section class='main'>
        <div class='toolbar'>
          <input v-model='selectedNote.title' placeholder='Note title'>
          <button @click='removeNote' title='Remove note'>
            <i class='material-icons'>
              delete
            </i>
          </button>
          <button
            @click='favoriteNote'
            title='Favorite note'>
            <i class='material-icons'>{{ selectedNote.favorite ? 'star' : 'star_border' }}</i>
          </button>
        </div>
        <textarea v-model="selectedNote.content"></textarea>
        <div class='toolbar status-bar'>
          <span class='date'>
            <span class='label'>Created</span>
            <span class='value'>{{ selectedNote.created_at | date }}</span>
          </span>
          <span class='lines'>
            <span class='label'>Lines</span>
            <span class='value>'>{{ linesCount }}</span>
          </span>
          <span class='words'>
            <span class='label'>Words</span>
            <span class='value>'>{{ wordsCount }}</span>
          </span>
          <span class='characters'>
            <span class='label'>Characters</span>
            <span class='value>'>{{ charsCount }} </span>
          </span>
        </div>
      </section>

      <aside class='preview' v-html="notePreview">
      </aside>
    </template>
  </div>
</template>

<script>
import marked from 'marked';

export default {
  data() {
    return {
      //content: localStorage.getItem('content') || 'You can write in markdown',
      notes: JSON.parse(localStorage.getItem('notes')) || [],
      selectedId: localStorage.getItem('selected-id') || null,
    }
  },

  computed: {
    notePreview() {
      return this.selectedNote ? marked(this.selectedNote.content) : ''
    },
    addButtonTitle() {
      return this.notes.length + ' note(s) already'
    },
    selectedNote() {
      return this.notes.find(note => note.id === this.selectedId)
    },
    sortedNotes() {
      return this.notes.slice()
        .sort((a,b) => a.created - b.created)
        .sort((a,b) => (a.favorite === b.favorite) ? 0 : a.favorite? -1 : 1)
    },
    linesCount() {
      if (this.selectedNote) {
        return this.selectedNote.content.split(/\r\n|\r|\n/).length
      }
    },
    wordsCount() {
      if (this.selectedNote) {
        var s = this.selectedNote.content
        s = s.replace(/\n/g, ' ')
        s = s.replace(/(^\s*)|(\s*$)/gi, '')
        s = s.replace(/\s\s+/gi, ' ')
        return s.split(' ').length
      }
    },
    charsCount() {
      if (this.selectedNote) {
        return this.selectedNote.content.split('').length
      }
    }
  },

  methods: {
    selectNote(note){
      this.selectedId = note.id
    },
    addNote(){
      const time = Date.now();
      const note = {
        id: String(time),
        title: 'New note ' + (this.notes.length + 1),
        content: '**Note** this is a notebook using markdown',
        created_at: time,
        favorite: false
      }
      this.notes.push(note)
    },
    removeNote(){
      if (this.selectedNote && confirm('Delete the note?')) {
        const index = this.notes.indexOf(this.selectedNote)
        if (index !== -1) {
          this.notes.splice(index,1)
        }
      }
    },
    favoriteNote(){
      this.selectedNote.favorite = !this.selectedNote.favorite
    },
    saveNotes(){
      localStorage.setItem('notes', JSON.stringify(this.notes))
    }
  },

  watch: {
    notes: {
      handler: 'saveNotes',
      deep: true,
    },
    selectedId(val) {
      localStorage.setItem('selected-id', val)
    },
  },
}
</script>

<style scoped>
</style>