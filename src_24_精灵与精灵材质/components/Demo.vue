<template>
    <div class="container">
        <div class="threeArea"></div>
        <div class="myStats"></div>
    </div>
</template>
  
<script lang="ts" setup>
import { onMounted, ref } from 'vue'
import * as THREE from "three"
import * as dat from 'dat.gui';
import Stats from "three/examples/jsm/libs/stats.module.js"
import { OrbitControls } from 'three/examples/jsm/controls/OrbitControls'
const scene = new THREE.Scene();
const camera = new THREE.PerspectiveCamera(75, window.innerWidth / window.innerHeight, 0.1, 1000);
camera.position.set(120, 60, 120);
const renderer = new THREE.WebGLRenderer();
const axesHelper = new THREE.AxesHelper(200);
renderer.setSize(window.innerWidth, window.innerHeight);
renderer.shadowMap.enabled = true;
const showThree: any = document.getElementsByClassName("threeArea");
const ctrl = new dat.GUI();
const path = process.env.BASE_URL;
onMounted(() => {
    showThree[0].appendChild(renderer.domElement);
})
for (let x = -30; x < 30; x++) {
    for (let y = -20; y < 20; y++) {
        for (let z = 0; z < 5; z++) {
            const spriteMat = new THREE.SpriteMaterial({
                transparent: true,
                opacity: 0.5,
                color: Math.random() * 0xffffff
            })
            const sprite = new THREE.Sprite(spriteMat)
            sprite.position.set(x * 4, y * 4, z * 100)
            scene.add(sprite)
        }
    }
}
const ambientLight = new THREE.AmbientLight(0xAAAAAA);

const addStats = () => {
    console.log('111');
    let stats = new Stats();
    stats.domElement.style.position = 'absolute';
    stats.domElement.style.left = '0';
    stats.domElement.style.top = '0';
    stats.setMode(0);
    document.body.appendChild(stats.domElement);
    return stats
}
let stats = addStats();

scene.add(axesHelper, ambientLight);
//创建轨道控制器
const controls = new OrbitControls(camera, renderer.domElement);
const renderRequest = () => {
    stats.update();
    renderer.render(scene, camera);
    requestAnimationFrame(renderRequest)
}
renderRequest();
const formatTooltip = (val: number) => {
    return val / 100
}
window.addEventListener('resize', () => {
    //重新渲染宽高比例
    camera.aspect = window.innerWidth / window.innerHeight
    // 手动更新相机的投影矩阵
    camera.updateProjectionMatrix();
    // 渲染器重新渲染
    renderer.setSize(window.innerWidth, window.innerHeight)
}, false)
</script>
<style scoped>
.threeArea {
    display: flex;
    justify-content: center;
}

.container {
    position: relative;
}

.slider-demo-block {
    display: flex;
    align-items: center;
    margin-left: 12%;
    width: 80vw;
    height: 20vh;
}

.slider-demo-block .el-slider {
    margin-top: 0;
    margin-left: 12px;
}

.slider-demo-block .demonstration {
    font-size: 14px;
    color: var(--el-text-color-secondary);
    line-height: 44px;
    flex: 1;
    overflow: hidden;
    text-overflow: ellipsis;
    white-space: nowrap;
    margin-bottom: 0;
}

.slider-demo-block .demonstration+.el-slider {
    flex: 0 0 70%;
}
</style>
  