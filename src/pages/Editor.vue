
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

let CodeMirrorGrammar = require("../../node_modules/codemirror_grammar.js");

var asmGrammar = {
  RegExpID: "RE::",

  Style: {
    comment: "comment",
    opcode1arg: "keyword",
    opcode0arg: "keyword",
    sectionText: "string",
    sectionData: "string",
    datacode: "keyword",
    number: "number"
  },

  Lex: {
    comment: { type: "comment", tokens: [[";", null]] },
    number: [
      "RE::/0x[0-9a-fA-F]+L?/",
      "RE::/0b[01]+L?/",
      "RE::/0o[0-7]+L?/",
      "RE::/[1-9]\\d*(e[\\+\\-]?\\d+)?L?/",
      "RE::/0(?![\\dx])/"
    ],
    opcode1arg: { autocomplete: true, tokens: ["LDA", "ADD", "SUB"] },
    opcode0arg: { autocomplete: true, tokens: ["OUT", "HLT", "NOP"] },
    datacode: { autocomplete: true, tokens: ["db"] },
    sectionText: { autocomplete: true, tokens: ["section .text"] },
    sectionData: { autocomplete: true, tokens: ["section .data"] },
    other: "RE::/\\S+/",
    "invalid_asm:error": "Invalid ASM",
    "noargs:error": "Invalid number of arguments",
    "@outofplace:error": "$1 not expected here"
  },

  Syntax: {
    opline0: "opcode0arg+ (number.error noargs)?",
    opline1: "opcode1arg+ number+",
    text:
      "sectionText (sectionText.error @outofplace | comment | opline0 | opline1)*",
    data:
      "sectionData (datacode number | comment | opcode1arg.error @outofplace | opcode0arg.error @outofplace)*",
    asm: "text data+"
  },

  Parser: [["asm"]]
};

// 2. parse the grammar into a Codemirror syntax-highlight mode
var asmMode = CodeMirrorGrammar.getMode(asmGrammar);

// 3. use it with Codemirror
CodeMirror.defineMode("asm", asmMode);

// enable user-defined code folding in the specification (new feature)
asmMode.supportCodeFolding = true;
CodeMirror.registerHelper("fold", asmMode.foldType, asmMode.folder);

// enable user-defined code matching in the specification (new feature)
asmMode.supportCodeMatching = true;
asmMode.matcher.options = { maxHighlightLineLength: 1000 }; // default
CodeMirror.defineOption("matching", false, function(cm, val, old) {
  if (old && old !== CodeMirror.Init) {
    cm.off("cursorActivity", asmMode.matcher);
    asmMode.matcher.clear(cm);
  }
  if (val) {
    cm.on("cursorActivity", asmMode.matcher);
    asmMode.matcher(cm);
  }
});

// enable syntax lint-like validation in the grammar
asmMode.supportGrammarAnnotations = true;
CodeMirror.registerHelper("lint", "asm", asmMode.validator);

// enable user-defined autocompletion (if defined)
asmMode.supportAutoCompletion = true;
CodeMirror.commands["my_autocompletion"] = function(cm) {
  CodeMirror.showHint(cm, asmMode.autocompleter, {
    prefixMatch: true,
    caseInsensitiveMatch: false
  });
};
// this also works (takes priority if set)
asmMode.autocompleter.options = {
  prefixMatch: true,
  caseInsensitiveMatch: true
};

import "codemirror/lib/codemirror.css";
// language js
// import "codemirror/mode/javascript/javascript.js";
// theme css
import "codemirror/theme/base16-dark.css";
export default {
  // name: 'PageName',
  components: {
    codemirror
  },
  data() {
    return {
      code: `section .text
  LDA 0x0a ; load accumulator
  ADD 0x0b
  ADD 0x0c
  SUB 0x0d
  OUT
  HLT
section .data
  ; line comment
  db 32
  db 28
  db 16`,
      cmOptions: {
        // codemirror options
        tabSize: 4,
        mode: "asm",
        theme: "base16-dark",
        lineNumbers: true,
        line: true,
        // simpleMode: true
        extraKeys: {
          "Ctrl-Space": "my_autocompletion",
          "Ctrl-L": "toggleComment"
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
