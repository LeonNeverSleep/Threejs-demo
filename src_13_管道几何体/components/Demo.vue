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
let points = [];
for (let i = 0; i < 10; i++) {
    let x = i * 3;
    let y = Math.sin(i) * 5;
    let z = i * 3;
    points.push(new THREE.Vector3(x, y, z))
}
points.forEach((item) => {
    const sphereGeo = new THREE.SphereGeometry(0.3)
    const sphereMat = new THREE.MeshBasicMaterial({ color: 0xff0000 })
    const sphere = new THREE.Mesh(sphereGeo, sphereMat)
    sphere.position.copy(item)
    scene.add(sphere)
})
let path = new THREE.CatmullRomCurve3(points)
const lineGeo = new THREE.BufferGeometry();
lineGeo.setFromPoints(path.getPoints(100))
const lineMat = new THREE.MeshBasicMaterial({ color: 0x00ff00 })
const line = new THREE.Line(lineGeo, lineMat)
scene.add(line)
let tubeGeo = new THREE.TubeGeometry(path, 50, 3, 30, false)
const tubeMat = new THREE.MeshBasicMaterial({ color: 0xcccccc, wireframe: true })
let tube = new THREE.Mesh(tubeGeo, tubeMat)
scene.add(tube)
const ctrlObj = {
    segments: 3,
    radius: 5,
    radialSegments: 30,
    isClosed: false
}
const tubeFolder = ctrl.addFolder("tubeFolder")
tubeFolder.add(ctrlObj, "segments", 1, 100).onChange((e) => {
    scene.remove(tube)
    tubeGeo = new THREE.TubeGeometry(path, ctrlObj.segments, ctrlObj.radius, ctrlObj.radialSegments, ctrlObj.isClosed)
    tube = new THREE.Mesh(tubeGeo, tubeMat)
    scene.add(tube)
})
tubeFolder.add(ctrlObj, "radius", 1, 10).onChange((e) => {
    scene.remove(tube)
    tubeGeo = new THREE.TubeGeometry(path, ctrlObj.segments, ctrlObj.radius, ctrlObj.radialSegments, ctrlObj.isClosed)
    tube = new THREE.Mesh(tubeGeo, tubeMat)
    scene.add(tube)
})
tubeFolder.add(ctrlObj, "radialSegments", 1, 20).onChange((e) => {
    scene.remove(tube)
    tubeGeo = new THREE.TubeGeometry(path, ctrlObj.segments, ctrlObj.radius, ctrlObj.radialSegments, ctrlObj.isClosed)
    tube = new THREE.Mesh(tubeGeo, tubeMat)
    scene.add(tube)
})
tubeFolder.add(ctrlObj, "isClosed").onChange((e) => {
    scene.remove(tube)
    tubeGeo = new THREE.TubeGeometry(path, ctrlObj.segments, ctrlObj.radius, ctrlObj.radialSegments, ctrlObj.isClosed)
    tube = new THREE.Mesh(tubeGeo, tubeMat)
    scene.add(tube)
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
  