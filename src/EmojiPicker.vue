<template>
  <div>
      <slot
        name="emoji-picker"
        :emojis="emojis"
        :insert="insert"
      ></slot>
  </div>
</template>

<script lang="ts">
  import Vue, { PropType } from 'vue'
  import emojis from './emojis'

  // https://developer.mozilla.org/en-US/docs/Web/JavaScript/Guide/Regular_Expressions#escaping
  const escapeRegExp = (s: string) => s.replace(/[.*+?^${}()|[\]\\]/g, '\\$&')

  export default /*#__PURE__*/Vue.extend({
    name: 'EmojiPicker',
    props: {
      search: {
        type: String as PropType<string>,
        required: false,
        default: '',
      },
      emojiTable: {
        type: Object as PropType<Record<string, Record<string, string>>>,
        required: false,
        default() {
          return emojis
        },
      },
    },
    computed: {
      emojis(): Record<string, Record<string, string>> {
        if (this.search) {
          const obj: Record<string, Record<string, string>> = {}

          for (const category in this.emojiTable) {
            obj[category] = {}

            for (const emoji in this.emojiTable[category]) {
              if (new RegExp(`.*${escapeRegExp(this.search)}.*`).test(emoji)) {
                obj[category][emoji] = this.emojiTable[category][emoji]
              }
            }

            if (Object.keys(obj[category]).length === 0) {
              delete obj[category]
            }
          }

          return obj
        }

        return this.emojiTable
      },
    },
    methods: {
      insert(emoji: string): void {
        this.$emit('emoji', emoji)
      },
    },
  })
  
</script>
