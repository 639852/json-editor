<template>
  <v-row>
    <v-col class="column">
      <v-btn
        dark
        color="success"
        :disabled="!isValid"
        @click="changeField"
      >
        Save
      </v-btn>
    </v-col>
    <v-col class="column-flex">
      <v-select
        required
        label="Choose key"
        v-model="key"
        :items="keys"
        :rules="[v => !!v || 'This field is required!']"
        @change="changeKeys"
      />
      <v-icon
        dark
        small
        color="red"
        v-if="key !== null"
        @click="cancel"
      >
        mdi-cancel
      </v-icon>
    </v-col>
    <v-col class="column">
      <v-btn
        dark
        color="error"
        :disabled="disabled"
        @click="deleteField"
      >
        Delete
      </v-btn>
    </v-col>
  </v-row>
</template>

<script>
export default {
  props: {
    isValid: { type: Boolean, required: true },
    disabled: { type: Boolean, required: true },
    changeField: { type: Function, required: true },
    deleteField: { type: Function, required: true },
    cancel: { type: Function, required: true }
  },
  data: () => ({
    key: null,
    keys: ['/']
  }),
  methods: {
    addKeys (tree, prevKey = '') {
      Object.entries(tree).forEach(([key, value]) => {
        if (typeof value === 'object') {
          this.keys.push(`${prevKey}${key}`)

          this.addKeys(value, `${key}/`)
        } else {
          this.keys.push(`${prevKey}${key}`)
        }
      })
    },
    changeKeys () {
      if (this.key === null) {
        this.$emit('changeKey', '')
      } else if (this.key.includes('/', 1)) {
        this.$emit('changeKey', this.key.split('/')[1])
      } else {
        this.$emit('changeKey', this.key)
      }
      this.$emit('changeDisable')

      const tree = JSON.parse(localStorage.getItem('JSON'))

      this.keys = ['/']
      this.addKeys(tree)
    }
  },
  mounted () {
    if (localStorage.getItem('JSON')) {
      const tree = JSON.parse(localStorage.getItem('JSON'))

      this.addKeys(tree)
    }
  },
  watch: {
    isValid () {
      if (localStorage.getItem('JSON')) {
        const tree = JSON.parse(localStorage.getItem('JSON'))

        this.keys = ['/']
        this.addKeys(tree)
      }
    }
  }
}
</script>

<style scoped>
.column {
  display: flex;
  align-items: center;
  justify-content: center;
}
</style>
