<template>
  <v-app>
      <googleKeep/>
  </v-app>
</template>

<script>
import googleKeep from './components/googleKeep'

export default {
  name: 'App',
  components: {
    googleKeep
  },
  data () {
    return {
      //
    }
  }
}
</script>