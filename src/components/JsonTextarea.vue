<template>
  <v-col>
    <v-form v-model="isValid">
      <v-textarea
        v-model="tree"
        :disabled="toggleNewFields"
        :rules="typeRule"
      ></v-textarea>
      <v-btn
        dark
        color="success"
        :disabled="!isValid || toggleNewFields"
        @click="changeField"
      >
        Save
      </v-btn>
    </v-form>
  </v-col>
</template>

<script>
export default {
  props: {
    toggleNewFields: { type: Boolean }
  },
  data: () => ({
    tree: '',
    isValid: true
  }),
  methods: {
    changeTree () {
      if (!localStorage.getItem('JSON')) return

      const brackets = []

      this.tree = localStorage.getItem('JSON')
        .split('')
        .map((letter) => {
          switch (letter) {
            case '{':
              brackets.push('{')
              return `{\n${'\t'.repeat(brackets.length)}`
            case '}':
              brackets.pop()
              return `\n${'\t'.repeat(brackets.length)}}`
            case ':':
              return ': '
            case ',':
              return `,\n${'\t'.repeat(brackets.length)}`
            default:
              return letter
          }
        })
        .join('')
    },
    changeField () {
      const tree = JSON.parse(this.tree)

      localStorage.setItem('JSON', JSON.stringify(tree))
      this.isValid = false
    }
  },
  mounted () {
    this.changeTree()
  },
  watch: {
    toggleNewFields () {
      this.changeTree()
    }
  },
  computed: {
    typeRule () {
      return [v => {
        try {
          return !!JSON.parse(v)
        } catch (e) {
          return e.message
        }
      }]
    }
  }
}
</script>
