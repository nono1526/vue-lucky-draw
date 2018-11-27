<template>
  <div id="app">
    <button
      @click="doUpload"
    >
      上傳
    </button>
    <button
      @click="randomPig"
    >
      抽獎
    </button>
    <div v-show="isEnd">
      恭喜!!得獎的是!!
    </div>
    <input
      type="file"
      ref="file"
      @change="handleUpload"
      multiple
    >
    <PhotoFrame
      msg="Halo nono 123 5566sss"
      :src="src"
    />
  </div>
</template>

<script>
import PhotoFrame from './components/PhotoFrame.vue'
export default {
  name: 'app',
  components: {
    PhotoFrame
  },
  data () {
    return {
      baseUrl: process.env.BASE_URL,
      src: '',
      images: [],
      count: 0,
      isEnd: ''
    }
  },
  methods: {
    doUpload () {
      this.$refs.file.click()
    },
    handleUpload (event) {
      const reader = new FileReader()
      const files = Array.from(event.target.files)
      files.forEach(file => {
        let reader = new FileReader()
        reader.addEventListener('load', () => {
          this.images.push(reader.result)
          this.src = reader.result
        })
        reader.readAsDataURL(file)
      })
    },
    randomPig (timeout) {
      this.isEnd = false
      this.src = this.images[this.getRandom(0, this.images.length)]
      this.count++
      if (this.count < 100) {
        setTimeout(() => this.randomPig(this.count * 1.5), timeout)
      } else {
        this.count = 0
        this.isEnd = true
      }
    },
    getRandom (min, max) {
      return Math.floor(Math.random() * (max - min)) + min
    }
  }
}
</script>

<style>
#app {
  font-family: 'Avenir', Helvetica, Arial, sans-serif;
  -webkit-font-smoothing: antialiased;
  -moz-osx-font-smoothing: grayscale;
  text-align: center;
  color: #2c3e50;
  margin-top: 60px;
}
input[type="file"] {
  display: none
}
</style>
