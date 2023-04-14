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
let cameraHeight = ref(20);
const scene = new THREE.Scene();
const camera = new THREE.PerspectiveCamera(75, window.innerWidth / window.innerHeight, 0.1, 1000);
camera.position.set(55, 55, 30);
const renderer = new THREE.WebGLRenderer();
const axesHelper = new THREE.AxesHelper(200);
renderer.setSize(window.innerWidth, window.innerHeight);
renderer.shadowMap.enabled = true;
const showThree: any = document.getElementsByClassName("threeArea");

const ctrlObj = {
    ambientColor: 0x000000,
    ambientIntensity: 1,
    spotColor: 0x000000,
    spotIntensity: 1
}
const ctrl = new dat.GUI();

onMounted(() => {
    showThree[0].appendChild(renderer.domElement);
})
//创建一个物体
const cubeGeometry = new THREE.BoxGeometry(8, 8, 8);
const cubeMaterial = new THREE.MeshLambertMaterial({ color: 0xffff00 });
const cube = new THREE.Mesh(cubeGeometry, cubeMaterial);
cube.position.set(15, 10, 20)
cube.castShadow = true;

const planeGeometry = new THREE.PlaneGeometry(300, 300)
const planeMaterial = new THREE.MeshLambertMaterial({ color: 0xcccccc })
const plane = new THREE.Mesh(planeGeometry, planeMaterial)
plane.rotation.x = -0.5 * Math.PI;
plane.position.set(20, 0, 0);
plane.receiveShadow = true;
const ambientLight = new THREE.AmbientLight(0xAAAAAA);
const spotLight = new THREE.SpotLight();
spotLight.position.set(-30, 40, -35);
spotLight.castShadow = true;
spotLight.shadow.mapSize = new THREE.Vector2(1024, 1024);
spotLight.shadow.camera.far = 130;
spotLight.shadow.camera.near = 40;
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
const ambientFolder = ctrl.addFolder("ambientLight");
ambientFolder.add(ctrlObj, "ambientIntensity", 0, 5)
ambientFolder.addColor(ctrlObj, "ambientColor").onChange((clr) => {
    ambientLight.color = new THREE.Color(clr);
})
const spotFolder = ctrl.addFolder("spotLight");
spotFolder.add(ctrlObj, "spotIntensity", 0, 5)
spotFolder.addColor(ctrlObj, "spotColor").onChange((clr) => {
    spotLight.color = new THREE.Color(clr);
})
const spotLightHelper = new THREE.SpotLightHelper(spotLight);
const cameraHelper = new THREE.CameraHelper(spotLight.shadow.camera);
scene.add(cube, axesHelper, plane, ambientLight, spotLight, spotLightHelper, cameraHelper);
//创建轨道控制器
const controls = new OrbitControls(camera, renderer.domElement);
const renderRequest = () => {
    stats.update();
    ambientLight.intensity = ctrlObj.ambientIntensity;
    spotLight.intensity = ctrlObj.spotIntensity;
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
  