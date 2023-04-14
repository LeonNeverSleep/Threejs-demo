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
import { ConvexGeometry } from 'three/examples/jsm/geometries/ConvexGeometry'
import { th } from 'element-plus/es/locale';
const scene = new THREE.Scene();
const camera = new THREE.PerspectiveCamera(75, window.innerWidth / window.innerHeight, 0.1, 1000);
camera.position.set(55, 55, 30);
const renderer = new THREE.WebGLRenderer();
const axesHelper = new THREE.AxesHelper(200);
renderer.setSize(window.innerWidth, window.innerHeight);
renderer.shadowMap.enabled = true;
const showThree: any = document.getElementsByClassName("threeArea");
const ctrl = new dat.GUI();

onMounted(() => {
    showThree[0].appendChild(renderer.domElement);
})
//创建一个物体
const cubeGeometry = new THREE.BoxGeometry(8, 8, 8);
const cubeMaterial = new THREE.MeshLambertMaterial({ color: 0xffff00 });
const cube = new THREE.Mesh(cubeGeometry, cubeMaterial);
cube.position.set(60, 30, -60)
cube.castShadow = true;
const ambientLight = new THREE.AmbientLight(0xcccccc);
let convexPointsArr = [];
for (let i = 0; i < 20; i++) {
    let x = Math.random() * 40 - 20;
    let y = Math.random() * 40 - 20;
    let z = Math.random() * 40 - 20;
    convexPointsArr.push(new THREE.Vector3(x, y, z))
}
convexPointsArr.forEach((item) => {
    const convexSphereGeo = new THREE.SphereGeometry(0.5);
    const convexSphereMat = new THREE.MeshBasicMaterial({ color: 0xff0000 });
    const convexPoint = new THREE.Mesh(convexSphereGeo, convexSphereMat);
    convexPoint.position.copy(item);
    scene.add(convexPoint)
})
const convexGeometry = new ConvexGeometry(convexPointsArr);
const convexMaterial = new THREE.MeshBasicMaterial({ color: 0x00ff00, wireframe: true });
const convex = new THREE.Mesh(convexGeometry, convexMaterial)
scene.add(convex);
let lathePointsArr = [];
for (let j = 5; j < 30; j++) {
    lathePointsArr.push(new THREE.Vector2(Math.sin(j * 0.2) * 10 + 5, (j - 5) * 2))
}
let latheGeo = new THREE.LatheGeometry(lathePointsArr,)
const latheMat = new THREE.MeshBasicMaterial({ color: 0x0000ff, wireframe: true })
let lathe = new THREE.Mesh(latheGeo, latheMat)
lathe.position.set(60, 30, 0)
scene.add(lathe)
const ctrlObj = {
    segments: 12,
    phiStart: 0,
    phiLength: Math.PI * 2
}
ctrl.add(ctrlObj, "segments", 1, 30).onChange(() => {
    scene.remove(lathe)
    latheGeo = new THREE.LatheGeometry(lathePointsArr, ctrlObj.segments, ctrlObj.phiStart, ctrlObj.phiLength)
    lathe = new THREE.Mesh(latheGeo, latheMat)
    lathe.position.set(60, 30, 0)
    scene.add(lathe)
})
ctrl.add(ctrlObj, "phiStart", 0, Math.PI * 2).onChange(() => {
    scene.remove(lathe)
    latheGeo = new THREE.LatheGeometry(lathePointsArr, ctrlObj.segments, ctrlObj.phiStart, ctrlObj.phiLength)
    lathe = new THREE.Mesh(latheGeo, latheMat)
    lathe.position.set(60, 30, 0)
    scene.add(lathe)
})
ctrl.add(ctrlObj, "phiLength", 0, Math.PI * 2).onChange(() => {
    scene.remove(lathe)
    latheGeo = new THREE.LatheGeometry(lathePointsArr, ctrlObj.segments, ctrlObj.phiStart, ctrlObj.phiLength)
    lathe = new THREE.Mesh(latheGeo, latheMat)
    lathe.position.set(60, 30, 0)
    scene.add(lathe)
})
const addStats = () => {
    let stats = new Stats();
    stats.domElement.style.position = 'absolute';
    stats.domElement.style.left = '0';
    stats.domElement.style.top = '0';
    stats.setMode(0);
    document.body.appendChild(stats.domElement);
    return stats
}
let stats = addStats();


scene.add(axesHelper);
//创建轨道控制器
const controls = new OrbitControls(camera, renderer.domElement);
const renderRequest = () => {
    stats.update();
    // pointLight.color = ctrlObj.pointColor;
    // pointLight.intensity = ctrlObj.pointIntensity;
    // pointLight.distance = ctrlObj.pointRadius;
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
  