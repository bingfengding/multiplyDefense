<template>
  <div>
    <header>
      <div class="row">
        <h1>Multiply Defense</h1>
      </div>
    </header>
    <section id="game" class="row">
      <gamescene></gamescene>
      <startscene></startscene>
    </section>
    <details class="how-to-play">
      <summary>玩法介绍</summary>
      <p>点击2个数字，2个数字相乘为掉落下的数字及可消除</p>
    </details>
  </div>
</template>
<script>
import gamescene from './gamescenecomponent.vue'
import startscene from './startscene.vue'
export default {
  data () {
    return {
    }
  },
  components: {
    gamescene,
    startscene
  }
}
</script>
<style scoped="scoped" lang="stylus">
  #game
    margin-top 10px
    position relative
    width 302px
    height 480px
    overflow hidden
  .how-to-play
    width 300px
</style>
