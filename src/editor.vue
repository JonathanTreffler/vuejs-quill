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
import _Quill from "quill";

const Quill = window.Quill || _Quill;


// generate font size whitelist
let fontSizeWhiteList = [];
for(let i = 5; i <= 100; i++) {
	fontSizeWhiteList.push(i+"px");
}

var Size = Quill.import("attributors/style/size");
Size.whitelist = fontSizeWhiteList;
Quill.register(Size, true);

const defaultOptions = {
	theme: "snow",
	boundary: document.body,
	modules: {
		toolbar: [
			["bold", "italic", "underline", "strike"],
			["blockquote", "code-block"],
			[{ "header": 1 }, { "header": 2 }],
			[{ "list": "ordered" }, { "list": "bullet" }],
			[{ "script": "sub" }, { "script": "super" }],
			[{ "indent": "-1" }, { "indent": "+1" }],
			[{ "direction": "rtl" }],
			[{ "size": ["small", false, "large", "huge"] }],
			[{ "header": [1, 2, 3, 4, 5, 6, false] }],
			[{ "color": [] }, { "background": [] }],
			[{ "font": [] }],
			[{ "align": [] }],
			["clean"],
			["link", "image", "video"],
			[{ "size": fontSizeWhiteList }],
		]
	},
	placeholder: "Insert text here ...",
	readOnly: false,
};

// pollfill
if (typeof Object.assign != "function") {
	Object.defineProperty(Object, "assign", {
		value(target) {
			if (target == null) {
				throw new TypeError("Cannot convert undefined or null to object");
			}
			const to = Object(target);
			for (let index = 1; index < arguments.length; index++) {
				const nextSource = arguments[index];
				if (nextSource != null) {
					for (const nextKey in nextSource) {
						if (Object.prototype.hasOwnProperty.call(nextSource, nextKey)) {
							to[nextKey] = nextSource[nextKey];
						}
					}
				}
			}
			return to;
		},
		writable: true,
		configurable: true
	});
}

// export
export default {
	name: "quill",
	data() {
		return {
			_options: {}, // eslint-disable-line vue/no-reserved-keys
			_content: "", // eslint-disable-line vue/no-reserved-keys
			defaultOptions,
			quill: _Quill,
		};
	},
	props: {
		content: Object,
		value: Object,
		disabled: {
			type: Boolean,
			default: false
		},
		options: {
			type: Object,
			required: false,
			default: () => ({})
		},
		globalOptions: {
			type: Object,
			required: false,
			default: () => ({})
		},
		focused: {
			type: Boolean,
		},
		defaultFont: {
			type: String,
		},
		defaultFontSize: {
			type: String,
		},
		toolbarDisabled: {
			type: Boolean,
			default: false,
		},
	},
	mounted() {
		this.initialize();
		this.$emit("assign:quill", this.quill);
	},
	beforeDestroy() {
		this.quill = null;
		delete this.quill;
	},
	methods: {
		// Init Quill instance
		initialize() {
			if (this.$el) {

				// Options
				this._options = Object.assign({}, this.defaultOptions, this.globalOptions, this.options);

				// Instance
				this.quill = new Quill(this.$refs.editor, this._options);

				this.quill.enable(false);

				// Set editor content
				if (this.value || this.content) {
					this.quill.setContents(this.value || this.content);
				}

				// Disabled editor
				if (!this.disabled) {
					this.quill.enable(true);
				}

				// Mark model as touched if editor lost focus
				this.quill.on("selection-change", () => {
					if(this.focused) {
						this.quill.focus();
					}
				});

				// Update model if text changes
				this.quill.on("text-change", () => {
					const quill = this.quill;
					let html = this.$refs.editor.children[0].innerHTML;

					if (html === "<p><br></p>") html = "";

					if(html == "") {
						this.applyDefaultStyle();
					}

					let contents = quill.getContents();

					this._content = contents;
					this.$emit("input", this._content);
					this.$emit("change", { contents, quill });
				});

				// Emit ready event
				this.$emit("ready", this.quill);

				this.applyDefaultStyle();
			}
		},
		applyDefaultStyle() {
			this.quill.format("font", this.defaultFont, "api");
			this.quill.format("font-size", this.defaultFontSize, "api");
		},
	},
	watch: {
		// Watch content change
		content(newVal) {
			if (this.quill) {
				if (newVal && newVal !== this._content) {
					this._content = newVal;
					const delta = this.quill.clipboard.convert(newVal);
					this.quill.setContents(delta);
				} else if(!newVal) {
					this.quill.setText("");
				}
			}
		},
		// Watch content change
		value(newVal) {
			if (this.quill) {
				if (newVal && newVal !== this._content) {
					this._content = newVal;
					this.quill.pasteHTML(newVal);
				} else if(!newVal) {
					this.quill.setText("");
				}
			}
		},
		// Watch disabled change
		disabled(newVal) {
			if (this.quill) {
				this.quill.enable(!newVal);
			}
		},
		focused(newVal) {
			if(newVal == true) {
				this.quill.focus();
			} else {
				this.quill.blur();
			}
		}
	}
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
