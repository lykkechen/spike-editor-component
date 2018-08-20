<template>
  <pre contenteditable="true" @input="updateHighlight" ref="editor"><code class="language-markdown"># 345</code></pre>
</template>

<script>
  /* eslint-disable */
  import Prism from 'prismjs'
  import "prismjs/prism"
  import 'prismjs/components/prism-markdown.js'

  function saveCaretPosition(context){
    var selection = window.getSelection();  //Returns a Selection object representing the range of text selected by the user or the current position of the caret.
    var range = selection.getRangeAt(0);  //The Selection.getRangeAt() method returns a range object representing one of the ranges currently selected.
    range.setStart(  context, 0 ); //The Range.setStart() method sets the start position of a Range.

// If the startNode is a Node of type Text, Comment, or CDATASection, 
// then startOffset is the number of characters from the start of startNode. 
// For other Node types, startOffset is the number of child nodes between the start of the startNode.
  
    var len = range.toString().length;

    return function restoreCaretPosition(){
      var pos = getTextNodeAtPosition(context, len);
      console.log('pos getTextNodeAtPosition', pos)
      selection.removeAllRanges();  // The Selection.removeAllRanges() method removes all ranges from the selection, leaving the anchorNode and focusNode properties equal to null and leaving nothing selected.
      
      var range = new Range();
      range.setStart(pos.node ,pos.position);
      selection.addRange(range); // A Range object that will be added to the Selection.
    }
  }

  function getTextNodeAtPosition(root, index){
    console.log('root', root)
    console.log('index', index)
    var lastNode = null;

    var treeWalker = document.createTreeWalker(root, NodeFilter.SHOW_TEXT, function next(elem) {
      if(index >= elem.textContent.length){
        index -= elem.textContent.length;
        lastNode = elem;
        return NodeFilter.FILTER_REJECT
      }
      return NodeFilter.FILTER_ACCEPT;
    });
    var c = treeWalker.nextNode();
    console.log('c treeWalker.nextNode', c)
    return {
      node:     c? c:     root,
      position: c? index: 0
    };
  }

  export default {
    name: 'MarkdownEditor',
    data() {
      return {
        editorElement: null
      }
    },
    mounted() {
      Prism.highlightAllUnder(this.$refs.editor)
    },
    methods: {
      updateHighlight() {
        var restoreCaretPosition = saveCaretPosition(this.$refs.editor);
        Prism.highlightAllUnder(this.$refs.editor)
        restoreCaretPosition();
      }
    }
  }
</script>

<style>
  .token.title {
    font-weight: bold;
    font-size: 1.5rem;
  }

  .token.bold {
    font-weight: bold;
  }

  .token.punctuation {
    font-weight: bold;
    color: grey;
  }
</style>
