<template>
  <div id="startScene" :class="isSure ? 'show' : 'hide'" @click = "startScene">
  </div>
</template>
<script>
import constant from './constant.vue'
export default {
  data () {
    return {
      isSure: true
    }
  },
  components: {
    constant
  },
  mounted () {
  },
  computed: {},
  methods: {
    startScene () {
      this.isSure = false
      constant.Event.$emit(constant.MSG_START_SCENE)
    }
  }
}
</script>
<style lang="stylus" scoped>
#startScene
  width 300px
  height 480px
  background url("../../static/images/start-scene-bg.png") no-repeat
  background-size 100% 100%
  transition all .5s ease-in
#startScene.hide
  display none
</style>
