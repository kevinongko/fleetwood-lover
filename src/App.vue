<script>
import { getClient, ResponseType } from '@tauri-apps/api/http'
import cheerio from 'cheerio'

export default {
  name: 'app',

  data () {
    return {
      password: ''
    }
  },

  mounted () {
    this.getPassword()
  },

  methods: {
    parseResponse (html) {
      const $ = cheerio.load(html)
      const text = cheerio.text($('.isi-dropdown > a:last-child'))
      return text.trim()
    },
    async getPassword () {
      const url = 'https://portal.gdn-commerce.com'
      const client = await getClient()
      const response = await client.get(url, {
        timeout: 30,
        responseType: ResponseType.Text
      })
      const result = await this.parseResponse(response.data)
      this.password = result.substring(17).toUpperCase()
    }
  }
}
</script>

<template>
  <div class="container">
    <div v-if="password">
      Today password is <br> <strong>{{ password }}</strong>
    </div>
    <div v-else>
      Connect to GDN Network
    </div>

    <button @click="getPassword">Reload</button>
  </div>
</template>

<style scoped>
.logo.vite:hover {
  filter: drop-shadow(0 0 2em #747bff);
}

.logo.vue:hover {
  filter: drop-shadow(0 0 2em #249b73);
}
</style>
