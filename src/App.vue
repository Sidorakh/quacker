<template>
  <v-app>
    

    <v-main>
      <quacker-chat/>
    </v-main>

    <!--
      <AppFooter />
    -->
  </v-app>
</template>

<script>
  import QuackerChat from './components/QuackerChat.vue';
  export default {
    components: {
      QuackerChat
    }
  }
</script>
