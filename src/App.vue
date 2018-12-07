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

      <div class="left__list">
        <h3>參加者</h3>
        <div class="list">
          <div v-for="img in images" :key="img.idx + img.name">
            {{ img.name }}
          </div>
        </div>
        <h3>得獎人</h3>
        <div class="list down__list">
          <div
            v-for="img in rewardList" :key="img.idx + img.name"
          >
            {{
              img.name
            }}
          </div>
        </div>
      </div>
      <div class="right__list main">
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
    <div class="flex">

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
      isEnd: false,
      audio: null,
      rewardAudio: null,
      rewardList: []
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
      let rndIndex = this.getRandom(0, this.images.length)
      console.log(rndIndex)
      this.src = this.images[rndIndex].src
      this.count++
      this.rewardAudio && this.rewardAudio.pause()
      if (this.count < 150) {
        setTimeout(() => {
          this.randomPig(this.count * 1.5)
          let audio = new Audio(`${this.baseUrl}/audio/change.mp3#t=0.5,0.55`)
          audio.play()
        }, timeout)
      } else {
        this.count = 0
        this.isEnd = true
        this.rewardAudio = new Audio(`${this.baseUrl}/audio/reward.mp3`)
        this.rewardAudio.currentTime = 0
        this.rewardAudio.play()
        this.rewardList.push(...this.images.splice(rndIndex, 1))
      }
    },
    getRandom (min, max) {
      return Math.floor(Math.random() * (max - min)) + min
    }
  },
  mounted () {
    console.log(this.baseUrl)
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
  margin: 10px;
  h3 {
    margin: 10px 0 5px 0;
    color: #fff;
  }
}
.list {
  border: 2px solid #f7f7f7;
  color: #fff;
  overflow: auto;
  background-color: rgba(#eaeaea, 0.1);
  height: 400px;
}
.down__list {
  margin-top: 10px;
  height: 200px
}
.right__list {
  flex: 1!important;
}
.main {
  margin: 10px;
}
</style>
