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
export default {
  props: ['link'],
  data: function () {
    return {
      youtubeRegexp: /(youtube\.com\/watch\?v=([^#&?]+)|youtu.be\/([^#&?]+))/i,
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
  }
}
</script>
<style scoped>
  img {
    width: 100%;
  }
</style>
