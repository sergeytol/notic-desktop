<template>
    <div>
        <b-container fluid class="screen chat" v-hotkey="keymap">
            <div class="topbar">
                <div class="row" align-h="between">
                    <div class="col-6">
                        <h4>P2P chat</h4>
                    </div>
                    <div class="col-6" style="text-align: right">
                        <b-button-group size="sm">
                            <b-btn variant="primary" @click="close()" title="Close (Esc)"><icon name="times"></icon></b-btn>
                        </b-button-group>
                    </div>
                </div>
            </div>
            <div class="content-wrap">
                <div class="chat" ref="chat" id="chat">
                    <b-row>
                        <b-col>Share your ID</b-col>
                        <b-col>or connect to another peer:</b-col>
                    </b-row>
                    <b-row>
                        <b-col>
                            <b-input-group class="mt-3">
                                <b-input size="sm" id="my-id" :value="chat.ownId"></b-input>
                                <b-input-group-append>
                                    <b-button size="sm" variant="primary">Copy <icon name="copy"></icon></b-button>
                                </b-input-group-append>
                            </b-input-group>
                        </b-col>
                        <b-col>
                            <b-input-group class="mt-3">
                                <b-input size="sm" id="peer-id" placeholder="Peer ID"></b-input>
                                <b-input-group-append>
                                    <b-button size="sm" variant="primary" @click="chatConnect">Connect <icon name="plug"></icon></b-button>
                                </b-input-group-append>
                            </b-input-group>
                            <b-form-text id="input-live-help">Status: <b-badge variant="danger">Dissconected</b-badge></b-form-text>
                        </b-col>
                    </b-row>
                    <b-row>
                        <b-col>
                            <b-input-group class="mt-3">
                                <b-input size="sm" id="peer-id" placeholder="Enter something..."></b-input>
                                <b-input-group-append>
                                    <b-button size="sm" variant="primary">Send <icon name="dove"></icon></b-button>
                                </b-input-group-append>
                            </b-input-group>
                        </b-col>
                    </b-row>
                    <b-row>
                        <b-col>
                            <div class="messages">

                            </div>
                        </b-col>
                    </b-row>
                </div>
            </div>
        </b-container>
    </div>
</template>

<script>
  export default {
    name: 'chat-page',
    computed: {
      keymap () {
        return {
          'esc': this.close
        }
      },
      chat () {
        return this.$store.getters.chat
      }
    },
    mounted () {
      if (this.$store.state.Store.appJustStarted) {
        this.$router.replace('/')
      }
    },
    methods: {
      close () {
        this.$router.replace('/')
      },
      chatConnect () {
        this.$parent.chatConnect()
      }
    }
  }
</script>

<style></style>