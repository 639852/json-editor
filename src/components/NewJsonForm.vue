<template>
  <v-form v-model="isValid" v-if="toggleNewFields" ref="form">
    <v-text-field
      required
      label="Key"
      v-model="newKey"
      v-if="mode ==='add'"
      :rules="generalRule"
    />
    <v-select
      required
      label="Choose type for value"
      v-model="typeValue"
      :items="typesValue"
      :rules="generalRule"
    />
    <v-select
      required
      label="Value"
      v-model="newValue"
      v-if="typeValue === 'boolean'"
      :items="['true', 'false']"
      :rules="generalRule"
    />
    <v-text-field
      label="Value"
      v-model="newValue"
      v-else
      :rules="typeRule"
    />
    <v-btn
      dark
      color="success"
      :disabled="!isValid"
      @click="changeField"
    >
      Save
    </v-btn>
  </v-form>
</template>

<script>
export default {
  props: {
    toggleNewFields: { type: Boolean, required: true },
    generalRule: { type: Array, required: true },
    fieldKey: { type: String, required: true },
    mode: { type: String, required: true }
  },
  data: () => ({
    isValid: false,
    newKey: '',
    newValue: '',
    typeValue: '',
    typesValue: ['string', 'number', 'boolean', 'object']
  }),
  methods: {
    changeField () {
      const value = (this.typeValue === 'string')
        ? this.newValue
        : JSON.parse(this.newValue)
      let tree = JSON.parse(localStorage.getItem('JSON'))

      if (tree) this.findNode(tree, value)
      else tree = { [this.newKey]: value }

      localStorage.setItem('JSON', JSON.stringify(tree))
      this.$refs.form.reset()
      this.$emit('changeToggle')
    },
    findNode (tree, newValue) {
      if (this.fieldKey === 'New key') {
        tree[this.newKey] = newValue
        return
      }

      for (const [key, value] of Object.entries(tree)) {
        switch (this.fieldKey) {
          case key:
            if (this.mode === 'add') {
              tree[key][this.newKey] = newValue
            } else {
              tree[this.fieldKey] = newValue
            }
            return
          default:
            if (typeof value === 'object') {
              this.findNode(value, newValue)
            }
        }
      }
    }
  },
  computed: {
    typeRule () {
      return [v => {
        try {
          const value = (this.typeValue !== 'string') ? JSON.parse(v) : v

          switch (this.typeValue) {
            case typeof value:
              return !!v
            default:
              throw TypeError('Invalid value type!')
          }
        } catch (e) {
          return e.message
        }
      }]
    }
  }
}
</script>
