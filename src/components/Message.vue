<template>
    <div class="message-outer" v-bind:class="messageStyle">
        <div class="message">
          <div class="message-bubble">
              <span>{{ content }}</span>
          </div>
          <div class="message-share" v-if="links.length !== 0">
            <share-link v-for="(link, index) in links" v-bind:key="index" v-bind:link="link"></share-link>
          </div>
        </div>
        <div class="timestamp">
            <div>{{ senderName }}</div>
            <span>{{ datetime }}</span>
        </div>
    </div>
</template>

<script>
import ShareLink from './ShareLink'

export default {
  components: { ShareLink },
  props: ['timestamp', 'content', 'share', 'sender-name', 'from-me'],
  data: function () {
    return {
      linkRegexp: /(http(s)?:\/\/.)?(www\.)?[-a-zA-Z0-9@:%._+~#=]{2,256}\.[a-z]{2,6}\b([-a-zA-Z0-9@:%_+.~#?&//=]*)/g
    }
  },
  computed: {
    'datetime': function () {
      const datetime = new Intl.DateTimeFormat(undefined, { dateStyle: 'full', timeStyle: 'long' }).format(new Date(this.timestamp))
      return datetime
    },
    'links': function () {
      let links = []
      let contentLinks = this.content.match(this.linkRegexp)
      if (contentLinks) {
        links.push(...contentLinks)
      } else if (this.share !== undefined && this.share.link !== undefined) {
        links.push(this.share.link)
      }
      return links
    },
    'messageStyle': function () {
      let style = this.fromMe ? 'from-me' : ''
      style += this.links.length !== 0 ? ' has-share' : ''

      return style
    }
  }
}
</script>
<style scoped>
.message-outer {
  display: flex;
  flex-direction: row;
}
.message-outer.from-me {
  text-align: right;
  flex-direction: row-reverse;
}
.message {
  line-height: 16px;
  font-size: 11pt;
  max-width: 300px;
}
.message-share, .message-bubble {
  border-radius: 32px;
  padding: 10px 10px;
}
.message-outer.has-share .message-bubble {
  border-bottom-left-radius: 0;
  border-bottom-right-radius: 0;
}
.message-outer.has-share .message-share {
  border-top-left-radius: 0;
  border-top-right-radius: 0;
}
.message-share {
  border: 1px #e4e4e4 solid;
  margin-bottom: 1px;
  overflow:hidden;
}
.message-bubble {
  background-color: #f1f0f0;
  margin-bottom: 1px;
}
.message-outer.from-me .message-bubble {
  background-color: #fa3c4c;
  color: #ffffff;
}
.timestamp {
  font-size: 9pt;
  margin: .3em 1em;
}
</style>
