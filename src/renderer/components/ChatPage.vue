<template>
    <div>
        <b-container fluid class="screen chat" v-hotkey="keymap">
            <div class="topbar">
                <div class="row" align-h="between">
                    <div class="col-6">
                        <h4>P2P chat</h4>
                        <b-button @click="genKeyPair()">Gen Key Pair</b-button>
                    </div>
                    <div class="col-6" style="text-align: right">
                        <b-button-group size="sm">
                            <b-btn variant="primary" @click="close()" title="Close (Esc)"><icon name="times"></icon></b-btn>
                        </b-button-group>
                    </div>
                </div>
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
                            <b-input size="sm" placeholder="Peer ID" :value="chat.peerId" @input="inputPeerId($event)"></b-input>
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
                            <b-input size="sm"placeholder="Enter something..." @input="inputMessage($event)"></b-input>
                            <b-input-group-append>
                                <b-button size="sm" variant="primary" @click="sendMessage()">Send <icon name="dove"></icon></b-button>
                            </b-input-group-append>
                        </b-input-group>
                    </b-col>
                </b-row>
            </div>
            <div class="content-wrap">
                <div class="chat" ref="chat" id="chat">
                    <b-row>
                        <b-col>
                            <div class="chat-messages-area">
                                <ChatMessage v-for="(message, index) in chat.messages"
                                             :key="index"
                                             :time="343"
                                             :sender="message.from"
                                             :message="message.text"
                                ></ChatMessage>
                            </div>
                        </b-col>
                    </b-row>
                </div>
            </div>
        </b-container>
    </div>
</template>

<script>
  import ChatMessage from '../components/ChatPage/ChatMessage.vue'
  export default {
    name: 'chat-page',
    components: {ChatMessage},
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
      },
      inputPeerId (event) {
        this.$store.commit('setChatPeerId', event)
      },
      inputMessage (event) {
        this.$store.commit('setChatMessage', event)
      },
      sendMessage () {
        this.$parent.sendChatMessage()
      },
      genKeyPair () {
        var openpgp = require('openpgp')
        var options = {
          userIds: [{ name: 'Jon Smith', email: 'jon@example.com' }],
          rsaBits: 4096,
          passphrase: 'super long and hard to guess secret'
        }
        openpgp.generateKey(options).then((key) => {
          var privkey = key.privateKeyArmored
          var pubkey = key.publicKeyArmored
          var revocationCertificate = key.revocationCertificate
          console.log(privkey, pubkey, revocationCertificate)

          const encryptDecryptFunction = async () => {
            const privKeyObj = (await openpgp.key.readArmored(privkey)).keys[0]
            await privKeyObj.decrypt('super long and hard to guess secret')

            const options = {
              message: openpgp.message.fromText('Hello, World!'),
              publicKeys: (await openpgp.key.readArmored(pubkey)).keys,
              privateKeys: [privKeyObj]
            }

            openpgp.encrypt(options).then(ciphertext => {
              var encrypted = ciphertext.data
              console.log('ENCRYPTED', encrypted, ciphertext)
              return encrypted
            }).then(async encrypted => {
              const options = {
                message: await openpgp.message.readArmored(encrypted),
                publicKeys: (await openpgp.key.readArmored(pubkey)).keys,
                privateKeys: [privKeyObj]
              }

              openpgp.decrypt(options).then(plaintext => {
                console.log('DECRYPTED', plaintext.data)
                return plaintext.data
              })
            })
          }
          encryptDecryptFunction()
        })
      }
    }
  }
</script>

<style></style>
