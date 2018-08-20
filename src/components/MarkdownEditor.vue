<template>
  <pre contenteditable="true" @input="updateHighlight" ref="editor"><code class="language-markdown"># 345
789</code></pre>
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
   console.log('1 -- range string:', range.toString())
    var len = range.toString().length;

    return function restoreCaretPosition(){
      var pos = getTextNodeAtPosition(context, len);
      console.log('4--- pos getTextNodeAtPosition->', pos)
      selection.removeAllRanges();  // The Selection.removeAllRanges() method removes all ranges from the selection, leaving the anchorNode and focusNode properties equal to null and leaving nothing selected.
      
      var range = new Range();
      range.setStart(pos.node ,pos.position);
      console.log('restore range.setStart(pos.node ,pos.position)', range)
      console.log('restore range string:', range.toString())
      selection.addRange(range); // A Range object that will be added to the Selection.
    }
  }

  function getTextNodeAtPosition(root, index){
    console.log('root', root)
    console.log('index', index)
    var lastNode = null;

    var treeWalker = document.createTreeWalker(root, NodeFilter.SHOW_TEXT, function next(elem) {
      //The TreeWalker object represents the nodes of a document subtree and a position within them.
      if(index >= elem.textContent.length) {
        index -= elem.textContent.length;
        lastNode = elem;
        return NodeFilter.FILTER_REJECT
      }
      return NodeFilter.FILTER_ACCEPT;
    });
    console.log('2 --- treeWalker', treeWalker)
    var c = treeWalker.nextNode(); 
    // The TreeWalker.nextNode() method moves the current Node to the next visible node in the document order,
    // and returns the found node. It also moves the current node to this one. 
    // If no such node exists, returns null and the current node is not changed.    
    console.log('3 --- c treeWalker.nextNode->', c)
    // console.log('typeof c', typeof c) c is an object
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
