<template>
  <v-col>
    <v-btn
      dark
      class="mr-4"
      color="accent"
      :disabled="toggle"
      @click="addField"
    >
      Add +
    </v-btn>
    <v-btn
      dark
      class="mr-4"
      color="accent"
      :disabled="toggle"
      @click="editField"
    >
      Edit
    </v-btn>
    <v-btn dark color="error" v-if="toggle" @click="cancel">
      Cancel
    </v-btn>
    <v-select
      required
      label="Choose where to add or change"
      v-model="key"
      v-if="toggle"
      :items="keys"
      :rules="generalRule"
      @change="changeToggleNewFields"
    />
    <json-form
      ref="form"
      :toggleNewFields="toggleNewFields"
      :generalRule="generalRule"
      :fieldKey="fieldKey"
      :mode="mode"
      @changeToggle="cancel"
    />
  </v-col>
</template>

<script>
import JsonForm from './JsonForm'

export default {
  components: { JsonForm },
  props: {
    toggleNewFields: { type: Boolean, required: true }
  },
  data: () => ({
    toggle: false,
    key: '',
    keys: ['New key'],
    fieldKey: '',
    mode: 'add',
    generalRule: [v => !!v || 'This field is required!']
  }),
  methods: {
    addField () {
      this.keys = ['New key']
      this.toggle = true
      this.mode = 'add'

      if (localStorage.getItem('JSON')) {
        const tree = JSON.parse(localStorage.getItem('JSON'))

        this.addKeys(tree, false)
      }
    },
    editField () {
      const tree = JSON.parse(localStorage.getItem('JSON'))

      this.keys = []
      this.toggle = true
      this.mode = 'edit'

      this.addKeys(tree, true)
    },
    cancel () {
      this.key = ''
      this.toggle = false
      this.$emit('changeToggle', false)
    },
    changeToggleNewFields () {
      this.$emit('changeToggle', true)

      if (this.key.includes('/')) {
        this.fieldKey = this.key.split('/')[1]
      } else {
        this.fieldKey = this.key
      }
    },
    addKeys (tree, all, prevKey = '') {
      Object.entries(tree).forEach(([key, value]) => {
        if (typeof value === 'object') {
          this.keys.push(`${prevKey}${key}`)

          this.addKeys(value, all, `${key}/`)
        } else if (all) {
          this.keys.push(`${prevKey}${key}`)
        }
      })
    }
  }
}
</script>
