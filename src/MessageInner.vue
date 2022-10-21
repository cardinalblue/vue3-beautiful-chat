<template>
  <TextMessage
    v-if="message.type === 'text'"
    :message="message"
    :message-colors="messageColors"
    :message-styling="messageStyling"
    :show-confirmation-deletion="showConfirmationDeletion"
    :confirmation-deletion-message="confirmationDeletionMessage"
    @remove="$emit('remove')"
  >
    <template v-slot:default="scopedProps">
      <slot
        name="text-message-body"
        :message="scopedProps.message"
        :messageText="scopedProps.messageText"
        :messageColors="scopedProps.messageColors"
        :me="scopedProps.me"
      >
      </slot>
    </template>
    <template v-slot:text-message-toolbox="scopedProps">
      <slot name="text-message-toolbox" :message="scopedProps.message" :me="scopedProps.me"> </slot>
    </template>
  </TextMessage>

  <EmojiMessage v-else-if="message.type === 'emoji'" :data="message.data" />

  <FileMessage
    v-else-if="message.type === 'file'"
    :data="message.data"
    :message-colors="messageColors"
  />

  <TypingMessage v-else-if="message.type === 'typing'" :message-colors="messageColors" />

  <SystemMessage
    v-else-if="message.type === 'system'"
    :data="message.data"
    :message-colors="messageColors"
  >
    <slot name="system-message-body" :message="message.data"> </slot>
  </SystemMessage>

  <!-- Catch-all component -->
  <component
    :is="message.component"
    v-else-if="message.component"
    :data="message.data"
    :message-colors="messageColors"
  />
</template>

<script>
import TextMessage from './messages/TextMessage.vue'
import FileMessage from './messages/FileMessage.vue'
import EmojiMessage from './messages/EmojiMessage.vue'
import TypingMessage from './messages/TypingMessage.vue'
import SystemMessage from './messages/SystemMessage.vue'

export default {
  components: {
    TextMessage,
    FileMessage,
    EmojiMessage,
    TypingMessage,
    SystemMessage
  },
  props: {
    message: {
      type: Object,
      required: true
    },
    colors: {
      type: Object,
      required: true
    },
    messageStyling: {
      type: Boolean,
      required: true
    },
    showConfirmationDeletion: {
      type: Boolean,
      required: true
    },
    confirmationDeletionMessage: {
      type: String,
      required: true
    },
  },
  computed: {
    messageColors() {
      return this.message.author === 'me' ? this.sentColorsStyle : this.receivedColorsStyle
    },
    receivedColorsStyle() {
      return {
        color: this.colors.receivedMessage.text,
        backgroundColor: this.colors.receivedMessage.bg
      }
    },
    sentColorsStyle() {
      return {
        color: this.colors.sentMessage.text,
        backgroundColor: this.colors.sentMessage.bg
      }
    }
  }
}
</script>
