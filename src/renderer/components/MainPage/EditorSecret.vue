<template>
    <b-button-toolbar class="editor-secret" @input="modify()">
        <b-button-group size="sm">
            <div role="group">
                <b-form-input size="sm"
                              class="text-left small"
                              placeholder="Title"
                              @input="changeTitle(index, $event)"
                              :value="secret.title"
                              :state="secret.errorTitleEmpty"
                              aria-describedby="input-title-feeback"
                ></b-form-input>
                <b-form-feedback id="input-title-feedback">
                    Must be not empty
                </b-form-feedback>
            </div>
        </b-button-group>
        <b-button-group size="sm">
            <b-form-input :type="secret.visibility ? 'text' : 'password'"
                          size="sm"
                          class="text-left"
                          placeholder="Secret"
                          name="password"
                          data-toggle="password"
                          @input="changeSecret(index, $event)"
                          :value="secret.content"
            ></b-form-input>
        </b-button-group>
        <b-button-group size="sm">
            <div role="group">
                <b-form-input :type="secret.visibility ? 'text' : 'password'"
                              size="sm"
                              class="text-left"
                              placeholder="Repeat secret"
                              id="password"
                              name="password"
                              data-toggle="password"
                              :value="secret.contentRepeat"
                              @input="changeSecretRepeat(index, $event)"
                              :state="secret.errorNotEquals"
                              aria-describedby="input-content-feeback"
                ></b-form-input>
                <b-form-feedback id="input-content-feedback">
                    Not equal
                </b-form-feedback>
            </div>
        </b-button-group>
        <div class="btn-group" role="group" aria-label="Basic example">
            <b-button-group size="sm" class="sx-1">
                <b-btn @click="copySecret(secret.content)" title="Copy to clipboard"><icon name="copy"></icon></b-btn>
            </b-button-group>
            <b-button-group size="sm" class="sx-1">
                <b-btn @click="toggleVisibility(index)" :title="secret.visibility ? 'Show' : 'Hide'"><icon :name="secret.visibility ? 'eye-slash' : 'eye'"></icon></b-btn>
            </b-button-group>
            <b-button-group size="sm" class="sx-1">
                <b-btn class="btn-outline-secondary" @click="genSecret(index)" title="Generate strong password"><icon name="bolt"></icon></b-btn>
            </b-button-group>
            <b-button-group size="sm" class="sx-1">
                <b-btn @click="genSecretStrong(index)" title="Generate ultra strong password"><icon name="bolt"></icon></b-btn>
            </b-button-group>
            <b-button-group size="sm" class="sx-1">
                <b-btn @click="deleteSecret(index)" title="Delete"><icon name="trash"></icon></b-btn>
            </b-button-group>
        </div>
    </b-button-toolbar>
</template>

<script>
  const {clipboard} = require('electron')
  export default {
    props: [
      'secret',
      'index'
    ],
    methods: {
      modify () {
        this.$store.dispatch('setNoteIsModified', true)
      },
      changeTitle (index, event) {
        this.$store.dispatch('editorUpdateSecretTitle', {index: index, data: event})
      },
      changeSecret (index, event) {
        this.$store.dispatch('editorUpdateSecretContent', {index: index, data: event})
      },
      changeSecretRepeat (index, event) {
        this.$store.dispatch('editorUpdateSecretContentRepeat', {index: index, data: event})
      },
      deleteSecret (index) {
        if (confirm('Are you sure you want to delete this secret?')) {
          this.$store.dispatch('editorDeleteSecret', index)
          this.modify()
        }
      },
      copySecret (text) {
        clipboard.writeText(text)
        this.$store.dispatch('startClipboardCountdown')
        this.$toast('✓ copied')
      },
      toggleVisibility (index) {
        this.$store.dispatch('toggleSecretVisibility', index)
      },
      genSecret (index) {
        if (confirm('Are you sure you want to regenerate this secret?')) {
          this.$store.dispatch('genSecret', index)
          this.modify()
        }
      },
      genSecretStrong (index) {
        if (confirm('Are you sure you want to regenerate this secret?')) {
          this.$store.dispatch('genSecretStrong', index)
          this.modify()
        }
      }
    }
  }
</script>

<style scoped>

</style>
