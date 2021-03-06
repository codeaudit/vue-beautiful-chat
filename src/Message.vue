<template>
  <div class="sc-message">
    <span v-if="message.author != 'me' && authorName" class="sc-message--name">{{ authorName }}</span>
    <div class="sc-message--content" :class="{
        internal: message.data && message.data.internal,
        sent: message.author === 'me',
        received: message.author === 'them',
        author: message.type === 'author',
        system: message.type === 'system',
      }">
      <TextMessage v-if="message.type === 'text' || message.type === 'longtext_response'" :data="message.data" :messageColors="determineMessageColors()" :onLinkClick="onLinkClick" />
      <LongTextMessage v-if="message.type === 'longtext'" :data="message.data" :messageColors="determineMessageColors()" />
      <EmojiMessage v-else-if="message.type === 'emoji'" :data="message.data" />
      <FileMessage v-else-if="message.type === 'file'" :data="message.data" :messageColors="determineMessageColors()" />
      <TypingMessage v-else-if="message.type === 'typing'" :messageColors="determineMessageColors()" />
      <AuthorMessage v-else-if="message.type === 'author'" :data="message.data" />
      <SystemMessage v-else-if="message.type === 'system'" :data="message.data" :messageColors="determineMessageColors()" />
      <ButtonMessage v-else-if="message.type === 'button'" :message="message" :data="message.data" :messageColors="determineMessageColors()" :onButtonClick="onButtonClick" />
      <ButtonResponseMessage v-else-if="message.type === 'button_response'" :data="message.data" :messageColors="determineMessageColors()" />
      <FormMessage v-else-if="message.type === 'webchat_form'" :message="message" :data="message.data" :messageColors="determineMessageColors()" :onFormButtonClick="onFormButtonClick" />
      <FormResponseMessage v-else-if="message.type === 'webchat_form_response'" :data="message.data" :messageColors="determineMessageColors()" />
      <ImageMessage v-else-if="message.type === 'image'" :data="message.data" :messageColors="determineMessageColors()" />
      <ListMessage v-else-if="message.type === 'list'" :message="message" :data="message.data" :messageColors="determineMessageColors()" :onButtonClick="onListButtonClick" />
      <DatetimeFakeMessage v-else-if="message.type === 'datetime'" :message="message" />
    </div>
    <span v-if="message.type !== 'datetime' && message.type !== 'typing' && message.type !== 'system' && message.type !== 'author'" class="sc-message--time-read">
      <template v-if="message.data && message.data.time && !message.data.hidetime">{{ message.data.time }}</template>
      <template v-if="read"> - Read</template>
    </span>
  </div>
</template>

<script>
import DatetimeFakeMessage from './DatetimeFakeMessage.vue'
import ListMessage from './ListMessage.vue'
import ImageMessage from './ImageMessage.vue'
import FormMessage from './FormMessage.vue'
import FormResponseMessage from './FormResponseMessage.vue'
import ButtonMessage from './ButtonMessage.vue'
import ButtonResponseMessage from './ButtonResponseMessage.vue'
import TextMessage from './TextMessage.vue'
import LongTextMessage from './LongTextMessage.vue'
import FileMessage from './FileMessage.vue'
import EmojiMessage from './EmojiMessage.vue'
import TypingMessage from './TypingMessage.vue'
import AuthorMessage from './AuthorMessage.vue'
import SystemMessage from './SystemMessage.vue'
import chatIcon from './assets/chat-icon.svg'

export default {
  data() {
    return {
      authorName: null,
    }
  },
  components: {
    DatetimeFakeMessage,
    ListMessage,
    ImageMessage,
    FormMessage,
    FormResponseMessage,
    ButtonMessage,
    ButtonResponseMessage,
    TextMessage,
    LongTextMessage,
    FileMessage,
    EmojiMessage,
    TypingMessage,
    AuthorMessage,
    SystemMessage,
  },
  props: {
    message: {
      type: Object,
      required: true
    },
    chatImageUrl: {
      type: String,
      default: chatIcon
    },
    colors: {
      type: Object,
      required: true
    },
    onButtonClick: {
      type: Function,
      required: true
    },
    onFormButtonClick: {
      type: Function,
      required: true
    },
    onListButtonClick: {
      type: Function,
      required: true
    },
    onLinkClick: {
      type: Function,
      required: true
    },
    read: {
      type: Boolean,
    }
  },
  created() {
    if (this.message.type == 'chat_open') return;

    if (this.message.user &&
        this.message.user.name &&
        this.message.user.name.length) {
      this.authorName = this.message.user.name;
    }
  },
  methods: {
    sentColorsStyle() {
      return {
        color: this.colors.sentMessage.text,
        backgroundColor: this.colors.sentMessage.bg
      }
    },
    receivedColorsStyle() {
      return {
        color: this.colors.receivedMessage.text,
        backgroundColor: this.colors.receivedMessage.bg
      }
    },
    determineMessageColors() {
      return this.message.author === 'me' ? this.sentColorsStyle() : this.receivedColorsStyle()
    }
  }
}
</script>
<style>
.sc-message {
  width: 300px;
  margin: auto;
  padding-bottom: 10px;
  display: flex;
  flex-direction: column;
}

.sc-message--content {
  width: 100%;
  display: flex;
}

.sc-message--content.sent {
  justify-content: flex-end;
}

.sc-message--name {
  font-size: x-small;
  margin-top: -5px;
  color: gray;
  text-align: left;
}

.sc-message--time-read {
  font-size: x-small;
  margin-bottom: -5px;
  color: gray;
}
.sc-message--content.sent + .sc-message--time-read {
  text-align: right;
}

.sc-message--content.system {
  justify-content: center;
}

.sc-message--content.read {
  justify-content: center;
}

.sc-message--content.sent .sc-message--avatar {
  display: none;
}

.sc-message--avatar {
  background-image: url(https://d13yacurqjgara.cloudfront.net/assets/avatar-default-aa2eab7684294781f93bc99ad394a0eb3249c5768c21390163c9f55ea8ef83a4.gif);
  background-repeat: no-repeat;
  background-size: 100%;
  background-position: center;
  min-width: 30px;
  min-height: 30px;
  border-radius: 50%;
  align-self: center;
  margin-right: 15px;
}

.sc-message--meta {
  font-size: xx-small;
  margin-bottom: -10px;
  color: white;
  text-align: center;
}

@media (max-width: 450px) {
  .sc-message {
    width: 80%;
  }
}

.sc-message--text {
  padding: 10px 12px;
  border-radius: 6px;
  font-weight: 400;
  font-size: 14px;
  line-height: 1.4;
  white-space: pre-wrap;
  -webkit-font-smoothing: subpixel-antialiased;
}
.sc-message--content.sent .sc-message--text {
  color: white;
  background-color: #4e8cff;
  max-width: calc(100% - 120px);
  word-wrap: break-word;
}

.sc-message--author {
  color: white;
  background-color: black;
}

.sc-message--author.sent {
  color: black;
  background-color: white;
  max-width: calc(100% - 120px);
  word-wrap: break-word;
}

.sc-message--content.received .sc-message--text {
  color: #263238;
  background-color: #f4f7f9;
  margin-right: 40px;
  max-width: calc(100% - 40px);
  word-wrap: break-word;
}
</style>
