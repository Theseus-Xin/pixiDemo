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
      img1: [],
      imgArr: [],
      spr2: null
    }
  },
  mounted() {
    this.img1 = ['/images/p1-p1.png']
    this.allTimeLine = gsap.timeline({ paused: true })
    this.getImg()
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
      // this.animatedSprite()
      this.alloyTouch()
      this.getTweenMax(spr)
      this.vortex()
    },
    getImg() {
      for (let i = 0; i < 52; i++) {
        let spr = PIXI.Texture.from(`/images/x${i + 1}.png`)
        let rect = new PIXI.Rectangle(0, 0, 1318, 1448)
        let imgSprArrItem = new PIXI.Texture(spr, rect)
        this.imgArr.push(imgSprArrItem)
      }
    },
    animatedSprite() {
      let animatedSpr = new PIXI.AnimatedSprite(this.imgArr)
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
          that.animationPlay(myProgress)
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
    },
    vortex() {
      this.spr2 = PIXI.Sprite.from(this.imgArr[0])
      this.spr2.position.set(375, 724)
      this.spr2.anchor.set(0.5, 0.5)
      this.spr2.alpha = 0
      this.app.stage.addChild(this.spr2)
      this.tweenMax2(this.spr2)
    },
    tweenMax2(spr2) {
      let t2 = gsap.timeline({ delay: 0.5 })
      let tw2 = gsap.to(spr2, { duration: 0.1, alpha: 1 })
      t2.add(tw2, 0)
      this.allTimeLine.add(t2, 0)
    },
    animationPlay(progress) {
      if (progress > 0.6) {
        let length = this.imgArr.length
        let i = Math.floor((progress - 0.6) / 0.3 * length)
        console.log('index', length);
        if (i < length) {
          console.log(`/images/x${i + 1}.png`);
          this.spr2.texture = PIXI.Texture.from(`/images/x${i + 1}.png`)
        }
      }
    }
  },
}
</script>
<style></style>