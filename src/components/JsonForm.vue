<template>
  <v-row>
    <v-col class="column-flex">
      <v-text-field
        required
        label="New key"
        v-if="toggleKeys"
        :value="newKey"
        :rules="generalRule"
        @change="(value) => changeValue('newKey', value)"
      />
      <v-text-field
        required
        label="Key"
        v-else
        :value="newKey"
        :rules="generalRule"
        @change="(value) => changeValue('newKey', value)"
      />
      <v-icon
        dark
        dense
        color="yellow"
        v-if="toggleKeys"
        @click="() => toggleKeys = false"
      >
        mdi-minus
      </v-icon>
      <v-icon
        dark
        dense
        color="green"
        v-else-if="typeValue === 'object'"
        @click="() => toggleKeys = true"
      >
        mdi-plus
      </v-icon>
    </v-col>
    <v-col>
      <v-select
        required
        label="Choose type for value"
        :value="typeValue"
        :items="typesValue"
        :rules="generalRule"
        @change="(value) => changeValue('typeValue', value)"
      />
    </v-col>
    <v-col>
      <v-select
        required
        label="Value"
        v-if="typeValue === 'boolean'"
        :value="newValue"
        :items="['true', 'false']"
        :rules="generalRule"
        @change="(value) => changeValue('newValue', value)"
      />
      <v-text-field
        disabled
        label="Value"
        value="{}"
        v-else-if="typeValue === 'object'"
      />
      <v-text-field
        label="Value"
        v-else
        :value="newValue"
        :rules="typeRule"
        @change="(value) => changeValue('newValue', value)"
      />
    </v-col>
  </v-row>
</template>

<script>
export default {
  props: {
    fieldKey: { type: String, required: true },
    newKey: { type: String, required: true },
    newValue: { type: String, required: true },
    typeValue: { type: String, required: true }
  },
  data: () => ({
    toggleKeys: false,
    typesValue: ['string', 'number', 'boolean', 'object'],
    generalRule: [v => !!v || 'This field is required!']
  }),
  methods: {
    changeValue (key, value) {
      this.$emit('changeValue', { key, value })

      if (value === 'object') {
        this.$emit('changeValue', { key: 'newValue', value: '{}' })
      }
    }
  },
  watch: {
    typeValue () {
      if (this.typeValue === '') {
        this.toggleKeys = false
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
