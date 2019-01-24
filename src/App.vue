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
      <div class="right__list main"  ref="fireworks-bg">
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
    <audio :src="`${this.baseUrl}/audio/change.mp3#t=0.5,0.55`" ref="change"></audio>
    <audio :src="`${this.baseUrl}/audio/reward.mp3`" ref="reward"></audio>
    <img class="right__bg-stamp__left" :src="`${this.baseUrl}/bg-stamp.png`" alt="">
    <img class="right__bg-stamp__right" :src="`${this.baseUrl}/bg-stamp.png`" alt="">

  </div>
</template>

<script>
import * as Fireworks from 'fireworks-canvas'
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
      src: `${process.env.BASE_URL}/bg-card.png`,
      images: [],
      count: 0,
      isEnd: false,
      audio: null,
      rewardAudio: null,
      rewardList: [],
      fireworks: null,
      fireworkShow: null
    }
  },
  computed: {
    theme () {
      return this.isEnd ? '#222' : '#AD0F0D'
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
        })
        reader.readAsDataURL(file)
      })
    },
    randomPig (timeout) {
      const SHOW_FIREWORK_TIME = 10
      const changeAudio = this.$refs.change
      const rewardAudio = this.$refs.reward
      this.isEnd = false
      let rndIndex = this.getRandom(0, this.images.length)
      this.src = this.images[rndIndex].src
      this.count++
      rewardAudio && rewardAudio.pause()
      this.stopFireworkShow()
      if (this.count < 100) { // continue do time change photo
        setTimeout(() => {
          this.randomPig(this.count * 1.5 + 50)
          changeAudio.currentTime = 1
          changeAudio.play()
        }, timeout)
      } else { // stop
        this.count = 0
        this.isEnd = true
        rewardAudio.currentTime = 0
        rewardAudio.play()
        this.rewardList.push(...this.images.splice(rndIndex, 1))
        this.startFireworkShow()
        // 煙火播放 10s
        window.setTimeout(() => this.stopFireworkShow(), 1000 * SHOW_FIREWORK_TIME)
      }
    },
    getRandom (min, max) {
      return Math.floor(Math.random() * (max - min)) + min
    },
    startFireworkShow () {
      this.fireworkShow = window.setInterval(() => this.fireworks.fire(), 200)
    },
    stopFireworkShow () {
      window.clearInterval(this.fireworkShow)
    }

  },
  mounted () {
    const options = {
      maxRockets: 20, // max # of rockets to spawn
      rocketSpawnInterval: 150, // millisends to check if new rockets should spawn
      numParticles: 100, // number of particles to spawn when rocket explodes (+0-10)
      explosionMinHeight: 0.5, // percentage. min height at which rockets can explode
      explosionMaxHeight: 1, // percentage. max height before a particle is exploded
      explosionChance: 0.13 // chance in each tick the rocket will explode
    }
    this.fireworks = new Fireworks(this.$refs['fireworks-bg'], options)
    const Things = this.fireworks.things.constructor.prototype
    const baseURL = this.baseUrl
    Things.explode = function (particle) {
      const audioArray = ['burst1.mp3', 'burst2.mp3', 'burst3.mp3', 'burst4.mp3']
      let randomBurst = Math.floor(Math.random() * 4)
      const audio = new Audio(`${baseURL}/audio/${audioArray[randomBurst]}`)
      audio.play()
      console.log(this.rockets)
      this.rockets--
      for (var i = 0; i < this.numParticles; i += 1) {
        this.add(particle.clone())
      }
      this['delete'](particle)
    }
  }
}
</script>

<style lang="scss">
* {
  box-sizing: border-box;
}
#app {
  transition: 1s;
  font-family: '微軟正黑體', 'Avenir', Helvetica, Arial, sans-serif;
  -webkit-font-smoothing: antialiased;
  -moz-osx-font-smoothing: grayscale;
  text-align: center;
  color: #DC9149;
  position: relative;
  padding: 30px;
  height: 100%;
  background-color: #AD0F0D;
}
input[type="file"] {
  display: none
}
html, body {
  padding: 0;
  margin:0;
  height: 100%;
  overflow: hidden;
}

.flex {
  display: flex;
  position: relative;
}
.left__list {
  flex: 0.2!important;
  margin: 10px;
  h3 {
    margin: 10px 0 5px 0;
    color: #DC9149;
  }
}
.list {
  border: 2px solid #DC9149;
  color: #e7e7e7;
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
  position: relative;
}
.main {
  margin: 10px;
}
canvas {
  position: absolute;
  top: 0;
  left: 0;
  z-index: 1;
}
.right__bg-stamp__left {
  position: absolute;
  top: 20%;
  left: 260px;
  height: 30%;
  transform: rotate(-30deg);
  opacity: .15;
}

.right__bg-stamp__right {
  position: absolute;
  top: 30%;
  right: 1%;
  height: 25%;
  transform: rotate(-165deg);
  opacity: .3;

}
</style>
