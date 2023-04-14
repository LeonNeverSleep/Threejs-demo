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
import { useNetwork } from '@vueuse/core';
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
const getTexture = () => {
    const texture = new THREE.TextureLoader().load(`${path}rain.png`)
    return texture
}
let verticles = [];
let velocities = [];
let range = 500;
for (let i = 0; i < 100000; i++) {
    //初始化雨滴粒子的时候给一个偏移量数组velocities
    velocities.push((Math.random() - 0.5) / 3, 0.1 + Math.random() / 5)
    const x = THREE.MathUtils.randFloatSpread(2000);
    const y = THREE.MathUtils.randFloatSpread(2000);
    const z = THREE.MathUtils.randFloatSpread(2000);
    verticles.push(x, y, z);

}
const cloudGeometry = new THREE.BufferGeometry();
cloudGeometry.setAttribute('position', new THREE.Float32BufferAttribute(verticles, 3))
cloudGeometry.setAttribute('velocity', new THREE.Float32BufferAttribute(velocities, 2))
const cloudMaterial = new THREE.PointsMaterial({
    size: 4,
    vertexColors: false,
    blending: THREE.AdditiveBlending,
    sizeAttenuation: true,
    // depthWrite: false,
    depthTest: false,
    map: getTexture(),
    // color: new THREE.Color(0xffffff),
})
const cloud = new THREE.Points(cloudGeometry, cloudMaterial)
scene.add(cloud)
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
    //取出所有粒子的位置和偏移量
    const pos_BufferAttr = cloud.geometry.getAttribute('position');
    const vel_BufferAttr = cloud.geometry.getAttribute('velocity');
    for (let i = 0; i < pos_BufferAttr.count; i++) {
        let pos_x = pos_BufferAttr.getX(i)
        let pos_y = pos_BufferAttr.getY(i)
        let vel_x = vel_BufferAttr.getX(i)
        let vel_y = vel_BufferAttr.getY(i)

        pos_x = pos_x - vel_x;
        pos_y = pos_y - vel_y;
        pos_BufferAttr.setX(i, pos_x)
        pos_BufferAttr.setY(i, pos_y)
        vel_BufferAttr.setX(i, vel_x)
        vel_BufferAttr.setY(i, vel_y)
    }
    pos_BufferAttr.needsUpdate = true;
    vel_BufferAttr.needsUpdate = true;
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
  