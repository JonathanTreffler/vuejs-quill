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
import vuejsQuillMixin from "./mixin.js";

export default {
	mixins: [vuejsQuillMixin],
};
</script>
<style>
	.quill-editor.toolbarDisabled .ql-toolbar {
		display: none;
    border: none !important;
	}
	.quill-editor.toolbarDisabled .ql-container {
		border-top: 1px solid #ccc !important;
	}
</style>
