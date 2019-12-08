<template>
    <div id="app"
         v-hotkey="keymap"
         @keyup="trackUsage()"
         @click="trackUsage()"
         @scroll="trackUsage()">
        <router-view></router-view>
        <div class="right-status-bar">
            <b-form-checkbox :checked="this.$store.state.Store.settings.darkTheme"
                             title="Dark theme (Ctrl+Shift+W)"
                             ref="darkThemeCheckbox"
                             value="1"
                             unchecked-value="0"
                             plain
                             @input="toggleDarkTheme($event)">
                dark
            </b-form-checkbox>
            <b-form-checkbox :checked="this.$store.state.Store.settings.windowOnTop"
                             title="Window on top (Ctrl+W)"
                             ref="windowOnTopCheckbox"
                             value="1"
                             unchecked-value="0"
                             plain
                             @input="toggleWindowOnTop($event)">
                top
            </b-form-checkbox>
        </div>
    </div>
</template>

<script>
  export default {
    name: 'notic-desktop',
    data () {
      return {
        chatConn: null
      }
    },
    computed: {
      keymap () {
        return {
          'ctrl+shift+w': this.changeDarkTheme,
          'ctrl+w': this.changeWindowOnTop
        }
      },
      chat () {
        return this.$store.getters.chat
      }
    },
    methods: {
      trackUsage () {
        this.$store.dispatch('trackUsage')
      },
      toggleWindowOnTop (event) {
        this.$store.dispatch('toggleWindowOnTop', parseInt(event))
      },
      changeWindowOnTop () {
        let windowOnTopBool = !!this.$refs.windowOnTopCheckbox.checked
        this.toggleWindowOnTop(+!windowOnTopBool)
      },
      toggleDarkTheme (event) {
        this.$store.dispatch('toggleDarkTheme', parseInt(event))
      },
      changeDarkTheme () {
        let darkThemeBool = !!this.$refs.darkThemeCheckbox.checked
        this.toggleDarkTheme(+!darkThemeBool)
      },
      setChatListeners (conn) {
        conn.on('data', (data) => {
          console.log('Received data:', data)
          console.log(this.chatConn)
          this.$store.commit('pushChatMessage', {
            from: this.chatConn.peer,
            text: data
          })
        })
        conn.on('close', () => {
          this.chatConn = null
        })
        conn.on('error', (err) => {
          console.error(err)
        })
      },
      chatConnect: function () {
        const conn = this.$peer.connect(this.chat.peerId, {
          label: 'user',
          metadata: {
            name: 'userName'
          },
          serialization: 'json'
        })
        conn.on('open', () => {
          conn.send('ping!')
          this.chatConn = conn
          this.setChatListeners(conn)
        })
      },
      sendChatMessage () {
        this.chatConn.send(this.chat.message)
        this.$store.commit('pushChatMessage', {
          from: 'Me',
          text: this.chat.message
        })
      }
    },
    created () {
      this.$peer.on('open', (id) => {
        this.$store.commit('setChatOwnId', id)
      })
      this.$peer.on('connection', (conn) => {
        this.chatConn = conn
        this.setChatListeners(conn)
        this.$store.commit('setChatPeerId', conn.peer)
        console.log(conn.peer, 'just connected')
      })
      this.$peer.on('close', () => {
        console.log('close')
      })
      this.$peer.on('disconnected', () => {
        console.log('disconnected')
      })
    }
  }
</script>

<style>
  /* CSS */
</style>
