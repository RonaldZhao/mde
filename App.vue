<template>
  <div id="app">
    <div id="editor"></div>
    <div id="preview"></div>
  </div>
</template>

<script>
import ace from 'ace-builds'
import 'ace-builds/src-noconflict/mode-markdown'
import 'ace-builds/src-noconflict/theme-dracula'
import 'ace-builds/src-noconflict/keybinding-vim'
var marked = require('marked')
var hljs = require('highlight.js')

export default {
  name: 'app',
  data() {
    return {
      aceTheme: 'ace/theme/dracula',
      aceMode: 'ace/mode/markdown',
    }
  },
  mounted: function() {
    var editor = ace.edit("editor", {
      fontSize: 14,
      theme: this.aceTheme,
      mode: this.aceMode,
      tabSize: 4,
      showPrintMargin: false,
      wrap: true,
      relativeLineNumbers: true,
    })
    editor.setKeyboardHandler('ace/keyboard/vim')
    // TODO: 编辑与预览的同步滚动
    // TODO: 文字多的时候反应慢，问题在预览上（太频繁？）
    // TODO: 根据 https://marked.js.org/#/USING_ADVANCED.md#highlight 看看能否优化
    hljs.initHighlightingOnLoad()
    editor.session.on('change', function() {
      document.getElementById("preview").innerHTML = marked(editor.getValue(),{
        smartLists: true,
        breaks: true,
        smartypants: true,
      })
      var blocks = document.getElementsByTagName("pre")
      for( var i = 0; i<blocks.length; i++) {
        hljs.highlightBlock(blocks[i])
      }
    })
  }
}
</script>

<style>
* {
    margin: 0px;
}
#app {
  font-family: 'Avenir', Helvetica, Arial, sans-serif;
  -webkit-font-smoothing: antialiased;
  -moz-osx-font-smoothing: grayscale;
}
#editor {
  position: absolute;
  width: 50%;
  height: 100%;
}
#preview {
  position: absolute;
  width: 50%;
  height: 100%;
  right: 0px;
  padding: 0.375rem 0.75rem;
  background-color: ivory;
  overflow-y: scroll;
  word-break: break-all;
  white-space: normal;
}
::-webkit-scrollbar {
  width: 8px;
}
::-webkit-scrollbar-track {
  border-radius: 3px;
  background: rgba(0, 0, 0, 0.075)
}
::-webkit-scrollbar-thumb {
  border-radius: 3px;
  background: #007bff
}
</style>
