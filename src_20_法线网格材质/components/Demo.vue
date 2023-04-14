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
camera.position.set(120, 30, 120);
const renderer = new THREE.WebGLRenderer();
const axesHelper = new THREE.AxesHelper(200);
renderer.setSize(window.innerWidth, window.innerHeight);
renderer.shadowMap.enabled = true;
const showThree: any = document.getElementsByClassName("threeArea");
const ctrl = new dat.GUI();
const path = process.env.BASE_URL;
const directionalLight = new THREE.DirectionalLight(0xffffff, 2);
directionalLight.castShadow = true;
directionalLight.shadow.mapSize.width = 2048;
directionalLight.shadow.mapSize.height = 2048;
directionalLight.position.set(100, 100, 100)
onMounted(() => {
    showThree[0].appendChild(renderer.domElement);
})
const cubeGeometry = new THREE.BoxGeometry(25, 25, 25);
const cubeMaterial = new THREE.MeshNormalMaterial();
const cube = new THREE.Mesh(cubeGeometry, cubeMaterial);
cube.position.set(15, 10, 20)
cube.castShadow = true;
const sphereGeo = new THREE.SphereGeometry(20)
const sphereMat = new THREE.MeshNormalMaterial()
const sphere = new THREE.Mesh(sphereGeo, sphereMat)
sphere.position.set(80, 10, 30)
sphere.castShadow = true;

const sphereGeo2 = new THREE.SphereGeometry(20)
const sphereMat2 = new THREE.MeshNormalMaterial()
const sphere2 = new THREE.Mesh(sphereGeo, sphereMat)
sphere2.position.set(0, 10, 80)
sphere2.castShadow = true;

const textureLoader = new THREE.TextureLoader();
textureLoader.load(`${path}aaa.png`, (map) => {
    // cubeMaterial.normalMap = map
    cubeMaterial.bumpMap = map
    cubeMaterial.needsUpdate = true;
    sphereMat.normalMap = map
    sphereMat.needsUpdate = true;
    sphereMat2.displacementMap = map;
    sphereMat2.needsUpdate = true;
})
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

scene.add(cube, sphere, sphere2, axesHelper, ambientLight, directionalLight);
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
  