<template>
  <div class="hello">
    <img
      class="photo"
      :class="{ending: isEnd}"
      :src="src"
    >
  </div>
</template>

<script>
export default {
  name: 'PhotoFrame',
  props: {
    src: {
      default: '',
      type: String
    },
    isEnd: {
      default: false,
      type: Boolean
    }
  }
}
</script>

<!-- Add "scoped" attribute to limit CSS to this component only -->
<style lang="scss" scoped>
h3 {
  margin: 40px 0 0;
}
ul {
  list-style-type: none;
  padding: 0;
}
li {
  display: inline-block;
  margin: 0 10px;
}
a {
  color: #42b983;
}
.photo {
  width: 462px;
  height: 819px;
  transition: 0.5s;
  transition-timing-function: ease;
  border: 10px solid #DC9149;
  border-radius: 30px;
  box-shadow: 0px 0px 20px 5px lighten(#DC9149, 30%);

}
.ending {
  transition: 1.2s;
  transition-timing-function: ease;
  transform: translateY(80px) scale(1.5) rotate(720deg);
  box-shadow: 0px 0px 150px 70px lighten(#DC9149, 30%);
}

</style>
