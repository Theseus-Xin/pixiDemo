<template>
  <div id="stage">
  </div>
</template>

<script>

import * as PIXI from 'pixi.js';
import * as PhyTouch from 'phy-touch';
import gsap from 'gsap';
export default {
  name: 'HelloWorld',
  props: {
    msg: String
  },
  data() {
    return {
      app: null,
      allTimeLine: null,
      img1: []
    }
  },
  mounted() {
    this.img1 = ['/images/p1-p1.png']
    this.allTimeLine = gsap.timeline({ paused: true })

    this.setup()
  },
  methods: {
    setup() {
      this.app = new PIXI.Application({
        width: 750,
        height: 1448
      })
      document.getElementById('stage').appendChild(this.app.view)
      let spr = PIXI.Sprite.from(this.img1[0])
      spr.position.set(375, 724)
      spr.anchor.set(0.5, 0.5)
      spr.alpha = 0
      this.app.stage.addChild(spr)
      this.animatedSprite()
      this.alloyTouch()
      this.getTweenMax(spr)
    },
    // animatedSprite
    animatedSprite() {
      let imgArr = []
      for (let i = 0; i < 52; i++) {
        let spr = PIXI.Texture.from(`/images/x${i + 1}.png`)
        let rect = new PIXI.Rectangle(0, 0, 1318, 1448)
        let imgSprArrItem = new PIXI.Texture(spr, rect)
        imgArr.push(imgSprArrItem)
      }
      let animatedSpr = new PIXI.AnimatedSprite(imgArr)
      animatedSpr.position.set(0, 0)
      animatedSpr.animationSpeed = 0.1
      animatedSpr.play()
      this.app.stage.addChild(animatedSpr)
    },
    // alloytouch
    alloyTouch() {
      const that = this
      let moveMin = -2000
      let phyTouch = new PhyTouch({
        touch: "body",//反馈触摸的dom
        vertical: true,//不必需，默认是true代表监听竖直方向touch
        min: moveMin, //不必需,运动属性的最小值
        max: 0, //不必需,滚动属性的最大值
        sensitivity: 1,//不必需,触摸区域的灵敏度，默认值为1，可以为负数
        bindSelf: false,
        maxSpeed: 0.1, //不必需，触摸反馈的最大速度限制
        value: 0,
        change: function (value) {
          let myProgress = value / moveMin
          console.log(myProgress);
          that.allTimeLine.seek(myProgress)
        },
      })
    },
    myFunction() {
      console.log('q234432');

    },
    getTweenMax(spr) {
      let t1 = gsap.timeline({ delay: 0.3 })
      let tweenMax1 = gsap.to(spr, { alpha: 1 })
      t1.add(tweenMax1, 0)
      this.allTimeLine.add(t1, 0)
    }
  },
}
</script>
<style>
#stage canvas {
  width: 100%;
}
</style>
