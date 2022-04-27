<template>
  <v-container>
    <v-row>
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
          :disabled="toggle || !tree || tree === '{}'"
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
        <new-json-form
          ref="form"
          :toggleNewFields="toggleNewFields"
          :generalRule="generalRule"
          :fieldKey="fieldKey"
          :mode="mode"
          @changeToggle="cancel"
        />
      </v-col>
      <new-json-textarea :toggleNewFields="toggleNewFields" />
    </v-row>
  </v-container>
</template>

<script>
import NewJsonForm from './NewJsonForm.vue'
import NewJsonTextarea from './NewJsonTextarea.vue'

export default {
  components: { NewJsonForm, NewJsonTextarea },
  data: () => ({
    tree: localStorage.getItem('JSON'),
    toggle: false,
    key: '',
    keys: ['New key'],
    fieldKey: '',
    mode: 'add',
    toggleNewFields: false,
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
      if (this.$refs.form.$el.reset) {
        this.$refs.form.$el.reset()
      }

      this.key = ''
      this.toggle = false
      this.toggleNewFields = false
    },
    changeToggleNewFields () {
      this.toggleNewFields = true

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
