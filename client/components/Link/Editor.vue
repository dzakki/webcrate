<template>
  <div>
    <bubble-menu v-if="editor" :editor="editor" class="bubble-menu">
      <Icon name="bold" :class="{ 'is-active': editor.isActive('bold') }" @click.native="editor.chain().focus().toggleBold().run()" />
      <Icon name="italic" :class="{ 'is-active': editor.isActive('italic') }" @click.native="editor.chain().focus().toggleItalic().run()" />
      <Icon name="strikethrough" :class="{ 'is-active': editor.isActive('strike') }" @click.native="editor.chain().focus().toggleStrike().run()" />
      <Icon name="code" :class="{ 'is-active': editor.isActive('code') }" @click.native="editor.chain().focus().toggleCode().run()" />
    </bubble-menu>
    <editor-content :editor="editor" />
  </div>
</template>

<script>
import { Editor, EditorContent, BubbleMenu } from '@tiptap/vue-2'
import StarterKit from '@tiptap/starter-kit'
import Placeholder from '@tiptap/extension-placeholder'
import TaskList from '@tiptap/extension-task-list'
import TaskItem from '@tiptap/extension-task-item'

export default {
	components: {
		EditorContent,
		BubbleMenu
	},

	props: {
		value: {
			type: String,
			default: ''
		},
		placeholder: {
			type: String,
			default: 'Edit text'
		}
	},

	data() {
		return {
			editor: null
		}
	},

	watch: {
		value(value) {
			// HTML
			const isSame = this.editor.getHTML() === value

			// JSON
			// const isSame = this.editor.getJSON().toString() === value.toString()

			if (isSame) {
				return
			}

			this.editor.commands.setContent(value, false)
		}
	},

	mounted() {
		this.editor = new Editor({
			extensions: [
				StarterKit,
				TaskList,
				TaskItem,
				Placeholder.configure({
					placeholder: this.placeholder
				})
			],
			content: this.value,
			onUpdate: () => {
				// HTML
				this.$emit('input', this.editor.getHTML())

				// JSON
				// this.$emit('update:modelValue', this.editor.getJSON())
			},
			editorProps: {
				attributes: {
					class: 'editor'
				}
			}
		})
	},

	beforeUnmount() {
		this.editor.destroy()
	}
}
</script>

<style lang="scss">
	/* Basic editor styles */
	.ProseMirror {
		> * + * {
			margin-top: 0.75em;
		}

		&.editor {
			outline: none;
		}

		ul,
		ol {
			padding: 0 1rem;
		}

		h1,
		h2,
		h3,
		h4,
		h5,
		h6 {
			line-height: 1.1;
		}

		code {
			background-color: var(--background-2nd);
			color: var(--text-light);
			padding: 0.2rem 0.2rem;
		}

		pre {
			background: #0D0D0D;
			color: #FFF;
			font-family: 'JetBrainsMono', monospace;
			padding: 0.75rem 1rem;
			border-radius: 0.5rem;

			code {
				color: inherit;
				padding: 0;
				background: none;
				font-size: 0.8rem;
			}
		}

		img {
			max-width: 100%;
			height: auto;
		}

		hr {
			margin: 1rem 0;
		}

		blockquote {
			padding-left: 1rem;
			border-left: 2px solid rgba(#0D0D0D, 0.1);
		}

		ul[data-type="taskList"] {
			list-style: none;
			padding: 0;

			li {
				display: flex;
				align-items: center;

				> label {
					flex: 0 0 auto;
					margin-right: 0.5rem;
				}
			}
		}
	}

	.ProseMirror p.is-editor-empty:first-child::before {
		content: attr(data-placeholder);
		float: left;
		color: var(--text-light);
		pointer-events: none;
		height: 0;
	}

	.bubble-menu {
		display: flex;
		border: 2px solid var(--grey);
		background-color: var(--background-2nd);
		padding: 0.5rem;
		border-radius: var(--border-radius);

		div {
			border: none;
			background: none;
			color: var(--text-dark);
			font-size: 0.85rem;
			font-weight: 500;
			margin: 0.2rem;
			opacity: 0.5;
			transition: opacity .2s ease;

			&:hover,
			&.is-active {
				opacity: 1;
				transition: none;
			}
		}
	}
</style>