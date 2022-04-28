<template>
  <v-app>
    <v-app-bar
      app
      dark
      dense
      color="accent"
    >
      <h2>JSON Editor</h2>
    </v-app-bar>

    <v-main>
      <v-container>
        <v-row>
          <json-editor
            :toggleNewFields="toggleNewFields"
            @changeToggle="(value) => toggleNewFields = value"
          />
          <json-textarea :toggleNewFields="toggleNewFields" />
        </v-row>
      </v-container>
    </v-main>
  </v-app>
</template>

<script>
import JsonEditor from './components/JsonEditor'
import JsonTextarea from './components/JsonTextarea'

export default {
  name: 'App',
  components: { JsonEditor, JsonTextarea },
  data: () => ({
    toggleNewFields: false
  })
}
</script>
