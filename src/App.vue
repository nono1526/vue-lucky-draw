<template>
  <div id="app">
    <div class="flex">
      <VBtn
        class="left__list"
        @click="doUpload"
      >
        載入
      </VBtn>
      <VBtn
        @click="randomPig"
      >
        抽獎
      </VBtn>
    </div>
    <div class="flex">
      <div class="left__list list">
        <div v-for="img in images" :key="img.idx + img.name">
          {{ img.name }}
        </div>
      </div>
      <div class="right__list main">
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
          :src="src"
          :isEnd="isEnd"
        />
      </div>
    </div>

    <canvas ref="bgCanvas"></canvas>
  </div>
</template>

<script>
import PhotoFrame from './components/PhotoFrame.vue'
import VBtn from './components/VBtn.vue'
export default {
  name: 'app',
  components: {
    PhotoFrame,
    VBtn
  },
  data () {
    return {
      baseUrl: process.env.BASE_URL,
      src: '',
      images: [],
      count: 0,
      isEnd: false
    }
  },
  methods: {
    doUpload () {
      this.$refs.file.click()
    },
    handleUpload (event) {
      const files = Array.from(event.target.files)
      files.forEach(file => {
        let reader = new FileReader()
        reader.addEventListener('load', () => {
          this.images.push({
            src: reader.result,
            name: file.name,
            idx: file.lastModified
          })
          this.src = reader.result
        })
        reader.readAsDataURL(file)
      })
    },
    randomPig (timeout) {
      this.isEnd = false
      this.src = this.images[this.getRandom(0, this.images.length)].src
      this.count++
      if (this.count < 150) {
        setTimeout(() => {
          this.randomPig(this.count * 1.5)
          let audio = new Audio(`${this.baseUrl}/audio/change.mp3#t=0.5,0.6`)
          audio.currentTime = 1
          audio.play()
        }, timeout)
      } else {
        this.count = 0
        this.isEnd = true
      }
    },
    getRandom (min, max) {
      return Math.floor(Math.random() * (max - min)) + min
    }
  },
  mounted () {
    const canvas = this.$refs.bgCanvas
    canvas.height = window.innerHeight
    canvas.width = window.innerWidth
    const ctx = canvas.getContext('2d')
    ctx.fillStyle = '#222'
    ctx.fillRect(0, 0, window.innerWidth, window.innerHeight)
  }
}
</script>

<style lang="scss">
* {
  box-sizing: border-box;
}
#app {
  font-family: '微軟正黑體', 'Avenir', Helvetica, Arial, sans-serif;
  -webkit-font-smoothing: antialiased;
  -moz-osx-font-smoothing: grayscale;
  text-align: center;
  color: #2c3e50;
  position: relative;
  padding: 30px;
}
input[type="file"] {
  display: none
}
html, body {
  padding: 0;
  margin:0;
  height: 100%;
}
canvas {
  display: block;
  position: absolute;
  top: 0;
  left: 0;
  z-index: -2;
}
.flex {
  display: flex;
}
.left__list {
  flex: 0.2!important;
}
.list {
  border: 2px solid #f7f7f7;
  margin: 10px;
  color: #fff;
  height: 500px;
  overflow: auto;
}
.right__list {
  flex: 1!important;
}
.main {
  margin: 10px;
}
</style>
