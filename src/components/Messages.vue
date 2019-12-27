<template>
    <div>
      <message
        v-for="(message, index) in filteredMessages"
        v-bind:key="index"
        v-bind:timestamp="message.timestamp_ms"
        v-bind:sender-name="message.sender_name"
        v-bind:from-me="message.sender_name == myUsername"
        v-bind:content="message.content"
        v-bind:share="message.share"
        ></message>
    </div>
</template>

<script>
import Message from './Message'
export default {
  components: { Message },
  data: () => {
    return {
      myUsername: '',
      allMessages: [],
      // filter: /spotify.com|soundcloud|vimeo/i
      filter: /youtube\.com|youtu\.be|spotify.com|soundcloud|vimeo/i
    }
  },
  computed: {
    filteredMessages: function () {
      return this.allMessages.filter((message) => {
        const hasSharedLink = message.share !== undefined && message.share.link !== undefined
        return this.filter.test(message.content) || (hasSharedLink && this.filter.test(message.share.link))
      })
    }
  },
  methods: {
    download_messages: function (jsonNumber = 1) {
      return this.$http.get(`./static/message_${jsonNumber}.json`).then(response => {
        let text = response.bodyText
        // fix utf-8 escaping ?!?!
        text = decodeURIComponent(escape(JSON.stringify(JSON.parse(text))))
        const json = JSON.parse(text)
        this.myUsername = json.participants[0].name
        if (this.allMessages) {
          this.allMessages = this.allMessages.concat(json.messages)
        } else {
          this.allMessages = json.messages
        }
        return this.download_messages(jsonNumber + 1)
      }, response => {
        // error
        console.log('Error fetching json file', response)
      })
    }
  },
  mounted () {
    this.download_messages().then(response => {
      console.log('end of loading')
    })
  }
}
</script>
