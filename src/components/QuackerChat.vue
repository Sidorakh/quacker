<template>
  <v-container class="fill-height">
    <v-responsive
      class="fill-height mx-auto chat-container"
      max-width="900"
      height="100%"
    >
    <v-toolbar>
      <v-toolbar-title>
        Quacker
      </v-toolbar-title>
      <v-spacer></v-spacer>
        <v-btn icon>
          <v-icon>mdi-cog</v-icon>
        </v-btn>
    </v-toolbar>
      <div class="chat-window" ref="chat">
        <template v-for="(msg,i) of history" :key="`msg-${i}`">
          <quacker-chat-bubble :text="msg.text" :side="msg.side"/>
        </template>
      </div>
      <div :class="`fill-width ${is_typing ? 'text-base' : 'text-transparent'} mb-1`">
        {{ is_typing ? 'Quacker is typing' : '.' }}
      </div>
      <v-text-field
        v-model="next_message"
        class="chat-input"
        append-icon-inner="mdi-send"
        @click:append-inner="send_message"
        @keydown.enter.exact.prevent="send_message"
        @keydown.enter.shift.exact.prevent="next_message += '\n'"
      />
    </v-responsive>
  </v-container>
</template>
<style @scoped>
  .chat-container {
    overflow-y: scroll;
    position: absolute,
  }
  .chat-input {
    position: sticky;
    top: 100%;
  }
  .chat-window {
    overflow-y: scroll;
    height: 75vh;
  }
</style>
<script>
import QuackerChatBubble from './QuackerChatBubble.vue';
  export default {
    name: 'QuackerChat',
    data: ()=>({
      history: [],
      next_message: '',
      quacks: [
        'Quack',
        'Quack quack',
        'Quack?',
        'Quack!',
        'Quack quack?',
        'Quack quack!'
      ],
      delay: 1000,
      is_typing: false,
      reply_timeout: null,
      typing_timeout: null,
    }),
    components: {
      QuackerChatBubble
    },
    methods: {
      send_message(){
        if (this.reply_timeout != null) {
          window.clearTimeout(this.reply_timeout);
        }
        if (this.typing_timeout != null) {
          window.clearTimeout(this.typing_timeout);
        }
        this.reply_timeout = setTimeout(this.reply_message,this.delay);
        this.typing_timeout = setTimeout(this.typing_message,this.delay/2);
        this.history.push({
          side: 'right',
          text: this.next_message,
        });
        this.next_message = '';
        this.$nextTick(()=>{
          this.$refs.chat.scrollTo(0,this.$refs.chat.scrollHeight);
        })
      },
      typing_message(){
        this.is_typing = true;
      },
      reply_message() {
        const quack = Math.floor(Math.random()*this.quacks.length);
        this.history.push({
          side: 'left',
          text: this.quacks[quack],
        })
        this.is_typing = false;
        this.$nextTick(()=>{
          this.$refs.chat.scrollTo(0,this.$refs.chat.scrollHeight);
        })
      }
    }
  }
</script>
