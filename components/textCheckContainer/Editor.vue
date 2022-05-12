<script setup>
/* eslint-disable @typescript-eslint/no-unused-vars */
import { useEditor, EditorContent } from "@tiptap/vue-3";
import StarterKit from "@tiptap/starter-kit";
import CharacterCount from "@tiptap/extension-character-count";
import _ from "lodash";

const props = defineProps({
  data: Array,
});

const correctText = useState("correctText", () => []);
const editorText = useState("editorText", () => "");
const editor = useEditor({
  content: "",
  disablePasteRules: true, // disable Markdown when pasting
  disableInputRules: true,
  extensions: [StarterKit, CharacterCount],
  editorProps: {
    attributes: {
      class:
        "w-full focus:ring focus:outline-none border border-gray-300 bg-gray-100 text-black min-h-[75vh] max-h-[82vh] overflow-y-auto mb-5",
    },
  },
  onUpdate({ editor }) {
    editorText.value = editor.getText({ blockSeparator: "\r\n\r\n" });
  },
});

const wrongLib = _.isEmpty(props.data) ? [] : props.data[0].values.flat();
const rightLib = _.isEmpty(props.data) ? [] : props.data[1].values.flat();

const check = (text, wrongDictionary, rightDictionary) => {
  const dictionaryReg = wrongDictionary.join("|");
  const splitText = text.split(new RegExp(`(${dictionaryReg})`));
  const fixText = splitText.map((word) => {
    const i = wrongDictionary.indexOf(word);
    if (i !== -1) {
      return [word, rightDictionary[i]];
    }
    return word;
  });
  correctText.value = fixText;
};
const undo = (wrongWord, rightWord) => {
  const i = correctText.value.findIndex((word) => word[1] === rightWord);

  correctText.value[i] = wrongWord;
};
const copy = () => {
  const finalText = correctText.value
    .map((item) => {
      if (!_.isArray(item)) {
        return item;
      }
      return item[1];
    })
    .join("");
  navigator.clipboard.writeText(finalText);
};
</script>

<template>
  <div v-if="data" class="w-full flex flex-col sm:flex-row">
    <div v-if="editor" class="w-full sm:w-1/2 mb-4">
      <div class="bg-gray-200 py-3 flex items-center">
        <button
          class="ml-4 mr-4 inline-flex items-center"
          :disabled="!editor.can().undo()"
          @click="editor.chain().focus().undo().run()"
        >
          <i class="ri-arrow-go-back-line ri-xl"></i>
        </button>
        <button
          class="ml-4 mr-4 inline-flex items-center"
          :disabled="!editor.can().redo()"
          @click="editor.chain().focus().redo().run()"
        >
          <i class="ri-arrow-go-forward-line ri-xl"></i>
        </button>
        <button
          class="ml-4 mr-4 inline-flex items-center"
          :disabled="!editor.can().clearContent()"
          @click="editor.chain().focus().clearContent().run()"
        >
          <i class="ri-delete-bin-5-line ri-xl"></i>
        </button>
        <button class="" @click="check(editorText, wrongLib, rightLib)">
          Check!
        </button>
      </div>
      <editor-content :editor="editor" />
      <div className="text-gray-300 text-right character-count">
        {{ editor?.storage && editor.storage.characterCount.characters() }}
        字
      </div>
    </div>
    <div class="pl-4 w-full sm:w-1/2">
      <div class="py-3">
        <button class="flex items-center" @click="copy()">
          <i class="ri-file-copy-2-line ri-xl"></i>
        </button>
      </div>
      <p class="whitespace-pre-wrap pt-2 max-h-[82vh] overflow-y-auto">
        <span
          v-for="(text, index) in correctText"
          :key="index"
          class="mb-5 leading-8"
          ><span v-if="!_.isArray(text)">{{ text }}</span
          ><span v-else class="bg-green-200 relative"
            >{{ text[1] }}
            <div
              class="absolute text-[8px] top-[-20px] left-0 flex min-w-[65px]"
            >
              <span class="text-red-400 line-through">{{ text[0] }}</span>
              <button class="pl-1" @click="undo(text[0], text[1])">
                <i class="ri-restart-line ri-xl"></i>
              </button></div></span
        ></span>
      </p>
    </div>
  </div>
</template>
