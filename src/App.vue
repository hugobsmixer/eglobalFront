<template>
  <v-app>
    <v-app-bar app color="primary" dark>
        <span class="mr-2">EGlobal Test</span>
    </v-app-bar>
    <v-main>
      <HelloWorld></HelloWorld>
    </v-main>
  </v-app>
</template>

<script>
import HelloWorld from './components/HelloWorld';

export default {
  name: 'App',

  components: {
    HelloWorld
  },

  data: () => ({
    //
  }),
};
</script>
