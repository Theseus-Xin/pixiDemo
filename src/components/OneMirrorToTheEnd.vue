<template>
    <div id="stage"></div>
    <div class="musicIcon" id="musicIcon"></div>
</template>

<script>

import * as PIXI from 'pixi.js';
import * as PhyTouch from 'phy-touch';
import gsap from 'gsap';
import {
    p1Arr, p2Arr, p2Arr2, p3Arr, p4Arr, p5Arr,
    spriteGroupBgObject,
    scene1Object,
    scene2Object,
    scene3Object,
    scene4Object,
    spriteGroupLastObject
} from "../utils/img"
export default {
    name: 'OneMirrorToTheEnd',
    data() {
        return {
            completed: false,
            stage: null,
            maxLong: -(9750 - 750),
            // 总时间轴
            allTimeLine: null
            // 加载素材，数组
        }
    },
    mounted() {
        this.preLoad()
        this.totalTime()
        // this.audioPause("bg")
        // this.audioPause("ding")
        // this.audioPause("huanhu")
    },
    methods: {

        // 加载动画素材
        preLoad() {
            const that = this
            PIXI.Assets.add('p1Arr', p1Arr)
            PIXI.Assets.add('p2Arr', p2Arr)
            PIXI.Assets.add('p2Arr2', p2Arr2)
            PIXI.Assets.add('p3Arr', p3Arr)
            PIXI.Assets.add('p4Arr', p4Arr)
            PIXI.Assets.add('p5Arr', p5Arr)
            PIXI.Assets.load(['p1Arr', 'p2Arr', 'p2Arr2', 'p3Arr', 'p4Arr', 'p5Arr'], function (progress) {
                // console.log(progress);
                if (progress == 1 && !that.completed) {
                    that.setup()
                }
            })
        },
        setup() {
            this.stage = new PIXI.Application({
                width: 750,
                height: 1448
            })
            document.getElementById('stage').appendChild(this.stage.view)
            this.completed = true
            this.createSprite()
        },
        // 创建精灵组，加载精灵
        createSprite() {
            this.initSprite({ sprName: 'spriteGroupBg', x: 0 })
            const spriteGroupScenes = this.initSprite({ sprName: 'spriteGroupScenes', x: 0 })
            this.initSprite({ sprName: 'scene1', x: 0, parent: spriteGroupScenes, pivotX: 1784, pivotY: 621 })
            this.initSprite({ sprName: 'scene2', x: 1773, parent: spriteGroupScenes, alpha: 0 })
            this.initSprite({ sprName: 'scene3', x: 4960, parent: spriteGroupScenes })
            this.initSprite({ sprName: 'scene4', x: 7902, parent: spriteGroupScenes })
            this.initSprite({ sprName: 'spriteGroupLast', x: -203 })
            // 添加精灵到精灵组
            let spritesObject = []
            spritesObject.push(...spriteGroupBgObject)
            spritesObject.push(...scene1Object)
            spritesObject.push(...scene2Object)
            spritesObject.push(...scene3Object)
            spritesObject.push(...scene4Object)
            spritesObject.push(...spriteGroupLastObject)

            spritesObject.map(v => {
                this.addSprite(v)
            })
            this.alloyTouch()
            this.tweenAction()
            this.star()
            this.house()
            this.scene2()
            this.note()
            this.window()
            this.work()
            this.showVortex()
        },
        initSprite({ sprName, x, parent = this.stage.stage, alpha = 1, pivotX, pivotY }) {
            let spr = new PIXI.Container()
            spr.name = sprName
            spr.alpha = alpha
            if (pivotX && pivotY) {
                spr.position.set(pivotX, pivotY)
                spr.pivot.set(pivotX, pivotY)
            } else {
                spr.position.set(x, 0)
            }
            parent.addChild(spr)
            return spr
        },

        addSprite({ img, x, y, alpha, sprName, sprGroup }) {
            const spr = PIXI.Sprite.from(img)
            spr.name = sprName
            spr.position.set(x, y)
            spr.alpha = alpha
            let sprArr = sprGroup.split('/')
            let len = sprArr.length
            let sprites = this.stage.stage.getChildByName(sprArr[0])
            for (let i = 1; i < len; i++) {
                sprites = sprites.getChildByName(sprArr[i])
            }
            sprites.addChild(spr)
        },


        // 识别滑动
        // ,seek,progress
        alloyTouch() {
            const that = this
            let phyTouch = new PhyTouch({
                touch: "body",//反馈触摸的dom
                vertical: true,//不必需，默认是true代表监听竖直方向touch
                min: that.maxLong, //不必需,运动属性的最小值
                max: 0, //不必需,滚动属性的最大值
                sensitivity: 1,//不必需,触摸区域的灵敏度，默认值为1，可以为负数
                bindSelf: false,
                maxSpeed: 0.8, //不必需，触摸反馈的最大速度限制
                value: 0,
                change: function (value) {
                    if (value > that.maxLong && value < 0) {
                        let myProgress = value / that.maxLong
                        // console.log(myProgress);
                        that.allTimeLine.seek(myProgress)
                        that.animationPlay(myProgress)
                        that.vortex(myProgress)
                    }
                },
            })
        },
        // 时间轴动画
        // gsap
        // 总时间轴
        totalTime() {
            this.allTimeLine = gsap.timeline({ paused: true })
        },
        // 子时间轴
        tweenAction() {
            let scenes = this.stage.stage.getChildByName('spriteGroupScenes')
            let t1 = gsap.timeline({ delay: 0 })
            let w1 = gsap.to(scenes.position, { duration: 1, x: this.maxLong })
            t1.add(w1, 0)
            this.allTimeLine.add(t1, 0)
        },

        // 序列帧动画
        // 星星显现"spriteGroupScenes/scene1/p1Star"
        star() {
            const star = this.stage.stage.getChildByName("spriteGroupScenes").getChildByName('scene1').getChildByName("p1Star")
            const startTime = -15 / this.maxLong
            const duringTime = -25 / this.maxLong
            const t1 = gsap.timeline({ delay: startTime })
            const w1 = gsap.to(star, { duration: duringTime, alpha: 1 })
            t1.add(w1, 0)
            this.allTimeLine.add(t1, 0)
        },
        // 房子放大"spriteGroupScenes/scene1/p1House"
        house() {
            const house = this.stage.stage.getChildByName("spriteGroupScenes").getChildByName('scene1')
            const startTime = -600 / this.maxLong
            const duringTime = -200 / this.maxLong
            const t1 = gsap.timeline({ delay: startTime })
            const w1 = gsap.to(house, { duration: duringTime, alpha: 0 })
            t1.add(w1, 0)
            this.allTimeLine.add(t1, 0)
        },
        scene2() {
            const scene2 = this.stage.stage.getChildByName("spriteGroupScenes").getChildByName('scene2')
            const startTime = -680 / this.maxLong
            const duringTime = -100 / this.maxLong
            const t1 = gsap.timeline({ delay: startTime })
            const w1 = gsap.to(scene2, { duration: duringTime, alpha: 1 })
            t1.add(w1, 0)
            this.allTimeLine.add(t1, 0)
        },
        // 音符飘动"spriteGroupScenes/scene2/p2Yinfu"
        note() {
            const note = this.stage.stage.getChildByName("spriteGroupScenes").getChildByName('scene2').getChildByName('p2Yinfu')
            const startTime = -2450 / this.maxLong
            const duringTime = -300 / this.maxLong
            const t1 = gsap.timeline({ delay: startTime })
            const w1 = gsap.to(note, { duration: duringTime, x: 3400, y: 300 })
            t1.add(w1, 0)
            this.allTimeLine.add(t1, 0)
        },
        // 小孩走路"spriteGroupScenes/scene2/p2Child"
        animationPlay(progress) {
            const step = -1000;
            const duringTime = (-2200 - step) / this.maxLong
            if (progress >= duringTime) {
                const len = p2Arr2.length
                const i = Math.floor((progress - step / this.maxLong) / duringTime * len)
                if (i < len) {
                    this.stage.stage.getChildByName("spriteGroupScenes").getChildByName('scene2').getChildByName('p2Child').texture = PIXI.Texture.from(p2Arr2[i])
                }
            }
        },
        // 黑夜缩小窗户"spriteGroupScenes/scene3/p32"
        window() {
            const note = this.stage.stage.getChildByName("spriteGroupScenes").getChildByName('scene3').getChildByName('p32')
            const startTime = -2580 / this.maxLong
            const duringTime = -400 / this.maxLong
            const t1 = gsap.timeline({ delay: startTime })
            const w1 = gsap.to(note, { duration: duringTime, x: 0, y: -20 })
            t1.add(w1, 0)
            this.allTimeLine.add(t1, 0)
        },
        // 工作中的小男孩
        work() {
            const work = this.stage.stage.getChildByName("spriteGroupScenes").getChildByName('scene3').getChildByName('p31')
            const startTime = -2780 / this.maxLong
            const duringTime = -600 / this.maxLong
            const t1 = gsap.timeline({ delay: startTime })
            const w1 = gsap.to(work, { duration: duringTime, alpha: 1 })
            t1.add(w1, 0)
            this.allTimeLine.add(t1, 0)
        },
        showVortex() {
            const vortex = this.stage.stage.getChildByName("spriteGroupLast").getChildByName('bgLast')
            const startTime = -7500 / this.maxLong
            const duringTime = -1500 / this.maxLong
            const t1 = gsap.timeline({ delay: startTime })
            const w1 = gsap.to(vortex, { duration: duringTime, alpha: 1 })
            t1.add(w1, 0)
            this.allTimeLine.add(t1, 0)
        },
        // 旋涡出现"spriteGroupLast/bgLast"
        vortex(progress) {
            const startTime = -7500 / this.maxLong
            const duringTime = -1500 / this.maxLong
            if (progress >= startTime) {
                console.log(progress);
                const len = p5Arr.length
                const i = Math.floor((progress - startTime) / duringTime * len)
                if (i < len && i >= 0) {
                    console.log(i);
                    this.stage.stage.getChildByName("spriteGroupLast").getChildByName('bgLast').texture = PIXI.Texture.from(p5Arr[i])
                }
            }
        },
        // 音频
        audioPlay(id) {
            let au = document.getElementById(id)
            au.play()
            document.addEventListener("WeixinJSBridgeReady", function () {
                au.play()
            })
        },
        audioPause(id) {
            let au = document.getElementById(id)
            au.pause()
            document.addEventListener("WeixinJSBridgeReady", function () {
                au.pause()
            })
        },
        // 加载页
    },
}
</script>
<style>
.audioBox {
    display: none;
}


.musicIcon {}

.mplay {}
</style>
