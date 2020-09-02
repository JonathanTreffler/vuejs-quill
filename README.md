[![npm](https://img.shields.io/npm/v/vuejs-quill?style=flat-square)](https://www.npmjs.com/package/vuejs-quill)
[![npm](https://img.shields.io/npm/dt/vuejs-quill?style=flat-square)](https://www.npmjs.com/package/vuejs-quill)
![GitHub Workflow Status](https://img.shields.io/github/workflow/status/TessyPowder/vuejs-quill/Lint?label=Lint&style=flat-square)
![GitHub](https://img.shields.io/github/license/TessyPowder/vuejs-quill?style=flat-square)
![GitHub last commit](https://img.shields.io/github/last-commit/TessyPowder/vuejs-quill?style=flat-square)
[![Maintenance](https://img.shields.io/maintenance/yes/2020?style=flat-square)](https://github.com/TessyPowder/vuejs-quill/commits/)

# Vuejs Quill

based on: [vue-quill-editor](https://github.com/surmon-china/vue-quill-editor/)

[Quill](https://github.com/quilljs/quill) editor component for Vue.

### Install

``` bash
npm install vuejs-quill --save
```

### Mount with component
``` javascript
import Vue from 'vue'
import VueQuillEditor from 'vuejs-quill'

import 'quill/dist/quill.core.css' // import styles
import 'quill/dist/quill.snow.css' // for snow theme
import 'quill/dist/quill.bubble.css' // for bubble theme

Vue.use(VueQuillEditor, /* { default global options } */)
```

 ### Mount with local component
```javascript
import 'quill/dist/quill.core.css'
import 'quill/dist/quill.snow.css'
import 'quill/dist/quill.bubble.css'

import { quillEditor } from 'vuejs-quill'

export default {
  components: {
    quillEditor
  }
}
```

### Mount with mixin
```vue
<template>
  <div
    v-on:click="$emit('activate')"
    class="quill-editor"
    :class="{'toolbarDisabled': toolbarDisabled == true, }"
  >
    <slot name="toolbar"></slot>
    <div ref="editor"></div>
  </div>
</template>

<script>
import { mixin } from "vuejs-quill";

export default {
	mixins: [mixin],
	data: function() {
		return {
			mixinOptions: {
				theme: "snow",
			}
		};
	},
};
</script>
```

### Register Quill module

```javascript
import Quill from 'quill'
import yourQuillModule from '../yourModulePath/yourQuillModule.js'
Quill.register('modules/yourQuillModule', yourQuillModule)

// Vue app...
```

### Component

``` vue
<template>
  <!-- Two-way Data-Binding -->
  <quill-editor
    ref="myQuillEditor"
    v-model="content"
    :options="editorOption"
    @blur="onEditorBlur($event)"
    @focus="onEditorFocus($event)"
    @ready="onEditorReady($event)"
  />

  <!-- Or manually control the data synchronization -->
  <quill-editor
    :content="content"
    :options="editorOption"
    @change="onEditorChange($event)"
  />
</template>

<script>
  // You can also register Quill modules in the component
  import Quill from 'quill'
  import someModule from '../yourModulePath/someQuillModule.js'
  Quill.register('modules/someModule', someModule)
  
  export default {
    data () {
      return {
        content: '<h2>I am Example</h2>',
        editorOption: {
          // Some Quill options...
        }
      }
    },
    methods: {
      onEditorBlur(quill) {
        console.log('editor blur!', quill)
      },
      onEditorFocus(quill) {
        console.log('editor focus!', quill)
      },
      onEditorReady(quill) {
        console.log('editor ready!', quill)
      },
      onEditorChange({ quill, html, text }) {
        console.log('editor change!', quill, html, text)
        this.content = html
      }
    },
    computed: {
      editor() {
        return this.$refs.myQuillEditor.quill
      }
    },
    mounted() {
      console.log('this is current quill instance object', this.editor)
    }
  }
</script>
```

### Quill
[Quill API document](https://quilljs.com/docs/quickstart/)

## Differences to surmon-china/vue-quill-editor
The Component Code is moved into a mixin, that can be used to create custom quill components without having to write all the basic wrapper code.
