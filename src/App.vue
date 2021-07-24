<template>
  <v-app>
    <v-app-bar
      app
      color="primary"
      dark
    >
    </v-app-bar>
    <v-main>
      <ImportFile/>
    </v-main>
  </v-app>
</template>

<script>
import ImportFile from './components/ImportFile';
export default {
  name: 'App',
  components: {
    ImportFile,
  },
};
</script>
