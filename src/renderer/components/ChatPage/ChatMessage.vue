<template>
    <div>
        <div :class="['chat-message', (sender == 'Me') ? 'sender' : '']" :id="'chat_message_index_' + index">
            <div class="title">{{sender + ', ' + time}}</div>
            <div class="body">
                {{message}}
            </div>
        </div>
    </div>
</template>

<script>
  export default {
    props: ['sender', 'index', 'message', 'time']
  }
</script>

<style scoped>

</style>
