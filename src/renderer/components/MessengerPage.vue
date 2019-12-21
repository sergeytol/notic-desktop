<template>
    <b-form>
        <b-container fluid class="screen messenger" v-hotkey="keymap">
            <div class="topbar">
                <div class="row" align-h="between">
                    <div class="col-6">
                        <h4>P2P messenger</h4>
                    </div>
                    <div class="col-6" style="text-align: right">
                        <b-button-group size="sm">
                            <b-btn variant="primary" @click="close()" title="Close (Esc)"><icon name="times"></icon></b-btn>
                        </b-button-group>
                    </div>
                </div>
            </div>
            <div class="content-wrap">

            </div>
        </b-container>
    </b-form>
</template>

<script>
  export default {
    name: 'messenger-page',
    computed: {
      keymap () {
        return {
          'esc': this.close
        }
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
      }
    }
  }
</script>

<style></style>
