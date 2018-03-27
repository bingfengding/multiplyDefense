<template>
  <div :class="isSure ? 'isShow' : 'isHide'">
    <canvas id="canvas" width="300" height="480"></canvas>
    <div id="control-box">
      <a href="javascript:void(0)" class="control" @click="controlClicked" v-for="n in 12" :data-value="n" :key="n">
        {{n}}
      </a>
    </div>
    <div id="game-over" :class="gameOverShow ? 'show' : 'hide'" @click="gameOverClicked"></div>
  </div>
</template>
<script>
import constant from './constant.vue'
export default {
  data () {
    return {
      isSure: false,
      stage: null,
      canvas: null,
      boxWidth: 50,
      boxHeight: 50,
      gameWidth: 300,
      gameHeight: 480,
      createjs: null,
      numberBoxs: [],
      fallingSpeed: 0.8,
      ticksPerNewBox: 80,
      calculatonText: null,
      controlHeight: 100,
      inputs: [],
      result: 1,
      boundaryY: 320,
      gameOverShow: false,
      initialLife: 3,
      lifes: 3,
      hearts: [],
      heartsContainer: null
    }
  },
  components: {
    constant
  },
  mounted () {
    constant.Event.$on(constant.MSG_START_SCENE, function () {
      this.isSure = true
      this.init()
      this.generateNumberBox()
      this.stage.update()
    }.bind(this))
  },
  methods: {
    //  重置
    init () {
      this.createjs = window.createjs
      this.canvas = document.getElementById('canvas')
      this.stage = new this.createjs.Stage(this.canvas)
      //   设置fps为40
      this.createjs.Ticker.framerate = 40
      //  设置了FPS之后，每次刷新Ticker都会向它的系统内发送1个消息：tick
      this.createjs.Ticker.addEventListener('tick', this.tick)
      //  乘法运算数字的位置与样式的初始化
      this.calculatonText = new this.createjs.Text('1 X 1 = 1', '18px Impact', 'black')
      this.calculatonText.textAlign = 'center'
      this.calculatonText.x = this.gameWidth / 2
      this.calculatonText.y = this.gameHeight - this.controlHeight - 30
      this.stage.addChild(this.calculatonText)
      let line = new this.createjs.Bitmap('../../static/images/line.png')
      line.y = this.boundaryY
      this.stage.addChild(line)
      this.initHearts()
    },
    initHearts () {
      this.heartsContainer = new this.createjs.Container()
      this.heartsContainer.x = 5
      this.heartsContainer.y = 5
      this.stage.addChild(this.heartsContainer)
      this.resetHearts()
    },
    resetHearts () {
      this.heartsContainer.removeAllChildren()
      this.hearts.length = 0
      for (let i = 0; i < this.initialLife; i++) {
        let heart = new this.createjs.Bitmap('../../static/images/heart.png')
        heart.x = i * 20
        this.heartsContainer.addChild(heart)
        this.hearts.push(heart)
      }
    },
    deduceLife () {
      this.lifes -= 1
      let heart = this.hearts[this.lifes]
      this.heartsContainer.removeChild(heart)
      if (this.lifes <= 0) {
        this.gameOver()
      }
    },
    gameOver () {
      //  paused=true让ticker不会在发送小心，因此函数就不会调用，那么整个循环就暂停
      this.createjs.Ticker.paused = true
      this.showGameOver()
    },
    showGameOver () {
      this.gameOverShow = true
    },
    hideGameOver () {
      this.gameOverShow = false
    },
    gameOverClicked () {
      this.lifes = this.initialLife
      this.removeAllNumberBoxes()
      this.hideGameOver()
      this.resetHearts()
      this.createjs.Ticker.paused = false
    },
    removeAllNumberBoxes () {
      for (let i = 0; i < this.numberBoxs.length; i++) {
        let box = this.numberBoxs[i]
        this.stage.removeChild(box)
      }
      this.numberBoxs.length = 0
    },
    updateText (string) {
      this.calculatonText.text = string
    },
    controlClicked (e) {
      let value = e.target.dataset.value
      let string = this.addInput(value)
      this.updateText(string)
      this.checkResult()
    },
    addInput (value) {
      if (this.inputs.length >= 2) {
        this.clearInputs()
      }
      this.inputs.push(value)
      this.result *= value
      return this.inputs.join(' X ') + ' = ' + this.result
    },
    clearInputs () {
      this.inputs.length = 0
      this.result = 1
    },
    tick (e) {
      this.stage.update()
      //  判断是否是暂停状态
      if (!e.paused) {
        this.moveObjects()
        //  获取回调的次数
        let ticksCount = this.createjs.Ticker.getTicks(true)
        //  FPS为40，所以每秒回调40次，因此设定为的80也就是2秒生成1个盒子
        if (ticksCount % this.ticksPerNewBox === 0) {
          this.generateNumberBox()
        }
      }
    },
    //  下移效果
    moveObjects () {
      let p = 0
      while (p < this.numberBoxs.length) {
        let box = this.numberBoxs[p]
        if (box.y > this.boundaryY) {
          this.removeNumberBox(box)
          this.deduceLife()
        } else {
          box.y += this.fallingSpeed
          p++
        }
      }
    },
    //  删除与result值相同的小盒子
    removeNumberBox (target) {
      for (let i = 0, len = this.numberBoxs.length; i <= len; i++) {
        let box = this.numberBoxs[i]
        if (box === target) {
          this.numberBoxs.splice(i, 1)
          this.stage.removeChild(box)
          return
        }
      }
    },
    findNumberBoxWithValue (value) {
      for (let i = 0, len = this.numberBoxs.length; i < len; i++) {
        let box = this.numberBoxs[i]
        if (box.value === value) {
          return box
        }
      }
    },
    //  获取与result值相同的小盒子进行删除
    checkResult () {
      let box = this.findNumberBoxWithValue(this.result)
      if (box) {
        this.showCircle(box.x, box.y)
        this.removeNumberBox(box)
        this.clearInputs()
      }
    },
    //  在找到的盒子位置显示出爆破效果
    showCircle (x, y) {
      let circle = new this.createjs.Bitmap('../../static/images/circle.png')
      circle.x = x || 0
      circle.y = y || 0
      this.stage.addChild(circle)
      this.createjs.Tween.get(circle).wait(500).to({alpha: 0}, 1000).call(function () { this.stage.removeChild(circle) }.bind(this)
      )
    },
    //  创建小盒子
    box () {
      let obj = new this.createjs.Container()
      let bitmap = new this.createjs.Bitmap('../../static/images/box.png')
      obj.addChild(bitmap)
      return obj
    },
    //  创建数字盒子
    numberBox (value) {
      let boxObj = this.box()
      let text = new this.createjs.Text(value, '24px Impact', 'red')
      text.textBaseline = 'middle'
      text.textAlign = 'center'
      text.x = this.boxWidth / 2
      text.y = this.boxHeight / 2
      boxObj.addChild(text)
      return boxObj
    },
    //  设定随机取值
    randomInt (min, max) {
      return Math.floor(Math.random() * (max - min + 1) + min)
    },
    //  设定数字盒子出现位置
    generateNumberBox () {
      let value = this.randomInt(1, 12) * this.randomInt(1, 12)
      let box = this.numberBox(value)
      box.value = value
      box.x = Math.random() * (this.gameWidth - this.boxWidth)
      box.y = 0
      this.stage.addChild(box)
      this.numberBoxs.push(box)
    }
  }
}
</script>
<style lang="stylus" scoped>
  .isHide
    display none
  .show
    display block
  .hide
    display none
  #game-over
    background url(../../static/images/replay.png)
    width 100px
    height 60px
    margin auto
    position absolute
    left 100px
    top 200px
  #game-over:hover
    background-image url(../../static/images/replay_hover.png)
  #game-over:active
    background-image url(../../static/images/replay_active.png)
  #canvas
    background: url(../../static/images/bg.png)
  #control-box
    width: 100%
    overflow: auto
    position: absolute
    bottom: 0
    .control
      display: block
      float: left
      width: 50px
      height: 50px
      background: url('../../static/images/input_button.png')
      text-align: center
      line-height: 50px
      font-size: 24px
      font-family: impact
    .control:active
      background: url('../../static/images/input_button_hover.png')
      color: red
</style>
