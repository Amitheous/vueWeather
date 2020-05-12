<template>
  <v-app>

    <v-content class='grey darken-3'>
      <Weather/>
    </v-content>
  </v-app>
</template>

<script>
import Weather from './components/Weather';

export default {
  name: 'App',

  components: {
    Weather,
  },
};
</script>
