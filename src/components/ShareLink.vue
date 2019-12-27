<template>
    <div>
      <a v-bind:href="link" target="_blank">
        <img v-bind:src="thumb">
        <span v-if="title">{{ title }}</span>
        <span v-else>{{ link }}</span>
      </a>
    </div>
</template>
<script>
import Vue from 'vue'
import secrets from '../../config/secrets.js'

let SpotifyToken = {
  promise: null,
  token: null,
  token_type: null,
  expires_in: null,
  get () {
    if (this.promise) {
      return this.promise
    } else {
      this.promise = Vue.http.post('https://accounts.spotify.com/api/token', {
        grant_type: 'client_credentials',
        client_id: secrets.SPOTIFY_CLIENT_ID,
        client_secret: secrets.SPOTIFY_CLIENT_SECRET
      }, {
        emulateJSON: true
      }).then(response => {
        this.token = response.body.access_token
        this.token_type = response.body.token_type
        this.expires_in = response.body.expires_in

        return Promise.resolve(this.token)
      }, response => {
        console.log('error spotify', response)
      })
      return this.promise
    }
  }
}

export default {
  props: ['link'],
  data: function () {
    return {
      youtubeRegexp: /(youtube\.com\/watch\?v=([^#&?]+)|youtu.be\/([^#&?]+))/i,
      spotifyRegexp: /open\.spotify\.com\/(track|album)\/([\w]+)/i,
      thumb: null,
      title: null
    }
  },
  mounted: function () {
    if (this.youtubeRegexp.test(this.link)) {
      const matches = this.link.match(this.youtubeRegexp)
      const videoId = matches[2] || matches[3]
      this.thumb = '//img.youtube.com/vi/' + videoId + '/0.jpg'

      // const detailsUrl = '//www.youtube.com/oembed?url=http://www.youtube.com/watch?v=' + videoId + '&format=json'
      // fix Access-Control-Allow-Origin
      const detailsUrl = 'https://noembed.com/embed?url=http://www.youtube.com/watch?v=' + videoId
      this.$http.get(detailsUrl).then(response => {
        this.title = response.body.title
      })
    }
    if (this.spotifyRegexp.test(this.link)) {
      const matches = this.link.match(this.spotifyRegexp)
      SpotifyToken.get().then(token => {
        const spotifyUrl = 'https://api.spotify.com/v1/' + matches[1] + 's/' + matches[2]
        const headers = {
          'Authorization': 'Bearer ' + token
        }
        if (matches[1] === 'track') {
          this.$http.get(spotifyUrl, {headers}).then(response => {
            let track = response.body
            this.thumb = track.album.images[0].url
            this.title = track.artists[0].name + ': ' + track.name
          })
        } else if (matches[1] === 'album') {
          this.$http.get(spotifyUrl, {headers}).then(response => {
            let album = response.body
            this.thumb = album.images[0].url
            this.title = album.artists[0].name + ': ' + album.name
          })
        }
      })
    }
  }
}
</script>
<style scoped>
  img {
    width: 100%;
  }
</style>
