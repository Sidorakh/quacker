<template>
    <div :class="`d-flex ${(side=='left' ? 'flex-row' : 'flex-row-reverse')}`">
      <v-sheet class="bubble" >
        {{ text }}
      </v-sheet>
    </div>
</template>
<style @scoped>
  .bubble {
    padding-top: 10px;
    padding-bottom: 10px;
    padding-left: 20px;
    padding-right: 20px;
    margin: 1em;
    border-radius: 1em !important;
    white-space: pre-wrap;
  }
</style>
<script>
  export default {
    name: 'QuackerChatBubble',
    props: {
      text: String,
      side: {
        validator(value,props) {
          return ['left','right'].includes(value);
        }
      },
    }
  }
</script>
