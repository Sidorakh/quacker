<template>
  <v-row>
    <v-col v-if="!$vuetify.display.mobile" cols="3"/>
    <v-col>
      <div class="d-flex flex-column main-container">
        <v-toolbar>
          <v-toolbar-title>
            Quacker
          </v-toolbar-title>
          <v-spacer></v-spacer>
          <v-menu>
            <template v-slot:activator="{props}">
              <v-btn icon v-bind="props">
                <v-icon>mdi-dots-vertical</v-icon>
              </v-btn>
            </template>
            <v-list>
                <v-list-item @click="export_chat">
                  <v-list-item-title> Export chat </v-list-item-title>
                </v-list-item>
                <v-list-item @click="show_options_dialog=true">
                  <v-list-item-title> Settings </v-list-item-title>
                </v-list-item>
              </v-list>
          </v-menu>
        </v-toolbar>
        <v-responsive>
          <div class="chat-window" ref="chat">
            <template v-for="(msg,i) of history" :key="`msg-${i}`">
              <quacker-chat-bubble :text="msg.text" :side="msg.side"/>
            </template>
          </div>
        </v-responsive>
        <v-row width="100%">
          <v-col cols="12" md="12">
            <div :class="`fill-width ${is_typing ? 'text-base' : 'text-transparent'} mb-1`">
              {{ is_typing ? 'Quacker is typing' : '.' }}
            </div>
            <v-text-field
              v-model="next_message"
              class="chat-input"
              append-inner-icon="mdi-send"
              @click:append-inner="send_message"
              @keydown.enter.exact.prevent="send_message"
            />
          </v-col>
        </v-row>
        <v-dialog v-model="show_options_dialog" max-width="500" persistent>
          <v-card>
            <v-card-title> Options </v-card-title>
            <v-card-text>
              <v-text-field type="number" label="Response delay" v-model="options.reply_delay"/>
              <v-text-field type="number" label="Typing delay" v-model="options.typing_delay"/>
            </v-card-text>
            <v-card-actions>
              <v-btn @click="save_and_close_options"> Save </v-btn>
              <v-btn> Discard </v-btn>
            </v-card-actions>
          </v-card>
        </v-dialog>
      </div>
    </v-col>
    <v-col v-if="!$vuetify.display.mobile" cols="3"/>
  </v-row>
</template>
<style @scoped>
  .chat-container {
    overflow-y: scroll;
    position: absolute,
  }
  .chat-input {
    position: sticky;
    top: 100%;
    height:72px;
  }
  .chat-window {
    overflow-y: scroll;
    height: 75dvh;
  }
  .fill-responsive-height {
    height: 100dvh;
    align-items: start;
    display: flex;
    flex-wrap: wrap;
  }
  .main-container {
    
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
      typing_delay: 100,
      reply_delay: 1000,
      is_typing: false,
      reply_timeout: null,
      typing_timeout: null,
      show_options_dialog: false,
      options: {
        typing_delay: 100,
        reply_delay: 1000,
      }
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
        this.reply_timeout = setTimeout(this.reply_message,this.reply_delay);
        this.typing_timeout = setTimeout(this.typing_message,this.typing_delay);
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
      },
      export_chat() {
        const chatlog = this.history.map(v=>`${v.side == 'left' ? 'Quacker' : 'You'}: ${v.text}`).join('\n\n');
        
        const el = document.createElement('a');
        el.setAttribute('download','chat.txt');
        el.setAttribute('href',`data:text/plain;base64,${btoa(chatlog)}`);
        el.click();
        el.remove();
      },
      open_options_dialog(){
        this.options.typing_delay = this.typing_delay;
        this.options.reply_delay = this.reply_delay;
      },
      save_and_close_options() {
        this.typing_delay = this.options.typing_delay;
        this.options.reply_delay =  this.options.reply_delay;
        this.show_options_dialog = false;
      },
      discard_and_close_options(){
        this.show_options_dialog = false;
      }
    }
  }
</script>
