
<template>
  <q-page padding>
    <codemirror v-model="code" :options="cmOptions"></codemirror>
  </q-page>
</template>

<script>
/* eslint-disable no-useless-escape */
import { codemirror } from "vue-codemirror";
import CodeMirror from "codemirror";
global.CodeMirror = CodeMirror;
import "codemirror/addon/mode/simple.js";
import "codemirror/addon/lint/lint.js";
import "codemirror/addon/lint/lint.css";
import "codemirror/addon/hint/show-hint.js";
import "codemirror/addon/hint/show-hint.css";
import "codemirror/addon/comment/comment.js";
import "codemirror/addon/fold/foldcode.js";
import "codemirror/addon/fold/foldgutter.js";
import "codemirror/addon/fold/foldgutter.css";

var asmParser = require("./asm.pegjs");

var exampleCode = require("./example.asm");

import asmMode from "./asm-mode.js";

CodeMirror.defineMode("asm", asmMode);

var dictionary = {
  text: ["LDA", "ADD", "SUB", "HLT", "OUT", "NOP", "section .data"],
  data: ["db", "dw"],
  none: ["section .text", "section .data"]
};

CodeMirror.registerHelper("hint", "dictionaryHint", function(editor) {
  var cur = editor.getCursor();
  var curLine = editor.getLine(cur.line);
  var start = cur.ch;
  var end = start;
  var state = editor.getTokenAt(editor.getCursor()).state;
  console.log(state);
  while (end < curLine.length && /[\w$]/.test(curLine.charAt(end))) ++end;
  while (start && /[\w$]/.test(curLine.charAt(start - 1))) --start;
  var curWord = start !== end && curLine.slice(start, end);
  var regex = new RegExp("^" + curWord, "i");
  console.log(dictionary[state.section]);
  return {
    list: (!curWord
      ? dictionary[state.section]
      : dictionary[state.section].filter(function(item) {
          return item.match(regex);
        })
    ).sort(),
    from: CodeMirror.Pos(cur.line, start),
    to: CodeMirror.Pos(cur.line, end)
  };
});

CodeMirror.commands.autocomplete = function(cm) {
  CodeMirror.showHint(cm, CodeMirror.hint.dictionaryHint);
};

CodeMirror.registerHelper("fold", "asm", function(cm, start) {
  function hasSection(lineNo, line) {
    var match = line && line.match(/^(section|segment)\s((\.text)|(\.data))+/);
    return match;
  }

  var firstLine = cm.getLine(start.line);
  var has = hasSection(start.line, firstLine);
  if (!has) return undefined;

  var lastLineNo = cm.lastLine();
  var end = start.line,
    nextLine = cm.getLine(end + 1);
  while (end < lastLineNo) {
    if (hasSection(end + 1, nextLine)) break;
    ++end;
    nextLine = cm.getLine(end + 1);
  }

  return {
    from: CodeMirror.Pos(start.line, firstLine.length),
    to: CodeMirror.Pos(end, cm.getLine(end).length)
  };
});

CodeMirror.registerHelper("lint", "asm", function(text) {
  var found = [];
  var lines;
  try {
    // console.log(asmParser.parse(text));
    lines = asmParser.parse(text);
  } catch (e) {
    console.log(e);
    found.push({
      from: CodeMirror.Pos(
        e.location.start.line - 1,
        e.location.start.column - 1
      ),
      to: CodeMirror.Pos(e.location.end.line - 1, e.location.end.column - 1),
      message: e.message
    });
    return found;
  }
  console.log(lines);

  // // state based errors
  // var sectionState = "";
  // var hasSectionText = false;
  // var validOps = { text: ["lda","add","sub","hlt","nop","out"], data: ["db"]};
  // for (var i = 0; i < lines.length; i++) {
  //   if (lines[i].type === "section") {
  //     sectionState = lines[i].section;
  //     if (lines[i].section === "text") hasSectionText = true;
  //   }
  //   if (!validOps[sectionState].includes(lines[i].op)) found.push({
  //     from
  //   })
  //   if (lines[i].type === "op") {
  //   }
  // }

  return found;
});

import "codemirror/lib/codemirror.css";
import "codemirror/theme/base16-dark.css";
export default {
  // name: 'PageName',
  components: {
    codemirror
  },
  data() {
    return {
      code: exampleCode,
      cmOptions: {
        tabSize: 2,
        mode: "asm",
        theme: "base16-dark",
        lineNumbers: true,
        line: true,
        extraKeys: {
          "Ctrl-Space": "autocomplete",
          "Ctrl-L": "toggleComment",
          "Ctrl-Q": function(cm) {
            cm.foldCode(cm.getCursor());
          }
        },
        lint: true,
        gutters: [
          "CodeMirror-lint-markers",
          "CodeMirror-linenumbers",
          "CodeMirror-foldgutter"
        ],
        foldGutter: true
      }
    };
  }
};
</script>

<style>
</style>
