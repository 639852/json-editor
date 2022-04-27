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

    <v-main class="container">
      <new-json />
    </v-main>
  </v-app>
</template>

<script>
import NewJson from './components/NewJson'

export default {
  name: 'App',
  components: { NewJson },
  data: () => ({
    //
  })
}
</script>
