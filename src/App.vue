<template>
  <div id="app">
    <img src="./assets/logo.png">
    <markdown-editor/>
  </div>
</template>

<script>
import MarkdownEditor from './components/MarkdownEditor.vue'

export default {
  name: 'app',
  components: {
    MarkdownEditor
  }
}
</script>

<style>
</style>
