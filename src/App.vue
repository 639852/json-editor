<template>
  <v-app>
    <v-app-bar app dark dense color="accent">
      <h2>JSON Editor</h2>
    </v-app-bar>

    <v-main>
      <v-form v-model="isValid" ref="form">
        <v-container>
          <v-row>
            <v-col cols="12">
              <json-textarea :disabled="disabled" @getTree="getTree" />
            </v-col>
          </v-row>
          <json-actions
            :isValid="isValid"
            :disabled="disabledDelete"
            :changeField="changeField"
            :deleteField="deleteField"
            :cancel="cancel"
            @changeKey="(value) => fieldKey = value"
            @changeDisable="changeFields"
          />
          <json-form
            :fieldKey="fieldKey"
            :newKey="newKey"
            :newValue="newValue"
            :typeValue="typeValue"
            :add="add"
            @changeValue="changeValue"
            @changeAdd="(value) => add = value"
          />
        </v-container>
      </v-form>
    </v-main>
  </v-app>
</template>

<script>
import JsonActions from './components/JsonActions'
import JsonForm from './components/JsonForm'
import JsonTextarea from './components/JsonTextarea'

export default {
  name: 'App',
  components: { JsonForm, JsonTextarea, JsonActions },
  data: () => ({
    tree: '',
    isValid: false,
    fieldKey: '',
    newKey: '',
    newValue: '',
    typeValue: '',
    disabled: false,
    disabledDelete: true,
    add: false
  }),
  methods: {
    changeValue ({ key, value }) {
      this[key] = value
    },
    getTree (tree) {
      try {
        JSON.parse(tree)

        this.tree = tree
        this.isValid = true
      } catch (e) {
        this.isValid = false
      }
    },
    deleteField () {
      if (this.fieldKey !== '/') {
        const tree = JSON.parse(localStorage.getItem('JSON'))

        this.deleteNode(tree)

        localStorage.setItem('JSON', JSON.stringify(tree))
      }

      this.cancel()
    },
    deleteNode (tree) {
      for (const [key, value] of Object.entries(tree)) {
        if (key === this.fieldKey) {
          delete tree[this.fieldKey]
          return
        } else if (typeof value === 'object') {
          this.deleteNode(value)
        }
      }
    },
    changeField () {
      if (this.tree !== '') {
        const tree = JSON.parse(this.tree)

        localStorage.setItem('JSON', JSON.stringify(tree))
        this.isValid = false
        this.tree = ''
        this.cancel()

        return
      }

      if (this.typeValue === 'object' && this.add) {
        this.newValue = '{}'
      }

      const value = (this.typeValue === 'string')
        ? this.newValue
        : JSON.parse(this.newValue)
      const tree = JSON.parse(localStorage.getItem('JSON'))

      this.findNode(tree, value)

      localStorage.setItem('JSON', JSON.stringify(tree))

      this.cancel()
    },
    findNode (tree, newValue) {
      if (this.fieldKey === '/') {
        tree[this.newKey] = newValue
        return
      }

      for (const [key, value] of Object.entries(tree)) {
        const isObject = typeof value === 'object'
        const isUndefined = newValue === undefined

        switch (this.fieldKey) {
          case key:
            if (isObject && !isUndefined && this.add) {
              tree[key][this.newKey] = newValue
            } else if (!isUndefined) {
              delete tree[this.fieldKey]

              tree[this.newKey] = newValue
            } else {
              return { key, value, type: typeof value }
            }
            return
          default:
            if (isObject) {
              return this.findNode(value, newValue)
            }
        }
      }
    },
    changeFields () {
      if (this.fieldKey === '/') {
        this.newKey = ''
        this.typeValue = ''
        this.newValue = ''
        this.disabledDelete = true
      } else {
        const tree = JSON.parse(localStorage.getItem('JSON'))
        const { key, value, type } = this.findNode(tree)

        this.newKey = key
        this.typeValue = type
        this.disabledDelete = false

        if (type === 'string') {
          this.newValue = value
        } else {
          this.newValue = JSON.stringify(value)
        }
      }

      this.disabled = true
    },
    cancel () {
      this.newKey = ''
      this.typeValue = ''
      this.newValue = ''
      this.$refs.form.reset()
      this.disabledDelete = true
      this.disabled = false
    }
  }
}
</script>

<style>
.column-flex {
  display: flex;
  justify-content: space-between;
  align-items: center;
  gap: 5px;
}
</style>
