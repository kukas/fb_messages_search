<template>
    <div>
      <div class="search-bar">
        <input v-model="filterInput" @change="filterMessages">
        <div v-if="filteredMessages.length > 0">
          Found {{ filteredMessages.length }} messages
        </div>
      </div>
      <message
        v-for="(message, index) in filteredMessages"
        v-bind:key="index"
        v-bind:timestamp="message.timestamp_ms"
        v-bind:sender-name="message.sender_name"
        v-bind:from-me="message.sender_name == myUsername"
        v-bind:content="message.content"
        v-bind:share="message.share"
        ></message>
        <div v-if="filteredMessages.length === 0">
          No messages found
        </div>
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
      filteredMessages: [],
      filterInput: 'spotify.com|soundcloud|vimeo'
      // filter: /spotify.com|soundcloud|vimeo/i
      // filter: /youtube\.com|youtu\.be|spotify.com|soundcloud|vimeo/i
    }
  },
  watch: {
    allMessages: function () {
      this.filterMessages()
    }
  },
  methods: {
    downloadMessages: function (jsonNumber = 1) {
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
        return this.downloadMessages(jsonNumber + 1)
      }, response => {
        // error
        console.log('Error fetching json file', response)
      })
    },

    filterMessages: function () {
      if (this.filterInput.length <= 1) {
        this.filteredMessages = []
      } else {
        let filter = new RegExp(this.filterInput, 'i')
        console.log(filter)
        this.filteredMessages = this.allMessages.filter((message) => {
          const hasSharedLink = message.share !== undefined && message.share.link !== undefined
          return filter.test(message.content) || (hasSharedLink && filter.test(message.share.link))
        })
      }
    }
  },
  mounted () {
    this.downloadMessages().then(response => {
      console.log('end of loading')
    })
  }
}
</script>
<style scoped>
.search-bar {
  padding-bottom: 3em;
  text-align: center;
}
.search-bar input {
  width: 400px;
  /* height: 30px; */
  padding: .5em;
  margin: .5em;
}
@media screen and (max-width: 400px) {
  .search-bar input {
    width: auto;
  }
}
</style>
