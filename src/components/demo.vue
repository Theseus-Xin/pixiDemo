<template>
    <div>
        <div class="box"></div>
    </div>
</template>

<script>
import gsap from "gsap";
export default {
    mounted() {
        gsap.to(".box", { x: 200 ,scaleX:2,scaleY:3})
    },
}
</script>

<style >
.box {
    width: 100px;
    height: 100px;
    background: #bc2e2e;
}
</style>