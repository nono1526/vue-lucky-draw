<template>
  <button
    class="button"
    v-bind="$attrs"
    v-on="$listeners"
  >
    <slot></slot>
  </button>
</template>

<script>
export default {
  name: 'v-btn'
}
</script>

<style lang="scss" scoped>
  .button {
    flex: 1 1 auto;
    margin: 10px;
    background-color: transparent;
    border-radius: 0px;
    border: 2px solid #DC9149;
    color: #DC9149;
    padding-top: 20px;
    padding-bottom: 20px;
    text-shadow: 0 0 1px rgba(0, 0, 0, 0.2), 0 1px 0 rgba(0, 0, 0, 0.2);
    font-size:18px;
    letter-spacing: .08em;
    font-weight: bold;
    outline:none;
    cursor: pointer;
    transition: .25s;
    overflow: hidden;
    position: relative;
    &:after {
      position: absolute;
      transition: .3s;
      content: '';
      width: 0;
      height: 0;
      top: 50%;
      left: 50%;
      background: #f7f7f7;
      border-radius: 50%;
      z-index: -1;
    }
  }
  .button:hover {
    border-radius: 10px;
    color: #AA2921;
     &:after {
      width: 200%;
      height: 200%;
      top: -50%;
      left: -50%;
    }
  }
</style>
