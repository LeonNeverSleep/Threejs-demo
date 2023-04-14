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
import { createMultiMaterialObject } from 'three/examples/jsm/utils/SceneUtils'
const scene = new THREE.Scene();
const camera = new THREE.PerspectiveCamera(75, window.innerWidth / window.innerHeight, 15, 1000);
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
const ctrlObj = {
    index: 0,
    cameraNear: 15,
    cameraFar: 1000,
    addCube: function () {
        const depthGeo = new THREE.BoxGeometry(10, 10, 10)
        const depthMat = new THREE.MeshDepthMaterial()
        const basicMat = new THREE.MeshBasicMaterial({
            color: this.color,
            transparent: true,
            blending: THREE.MultiplyBlending
        })
        const newCube = createMultiMaterialObject(depthGeo, [depthMat, basicMat])
        newCube.castShadow = true
        newCube.position.set(100 - 15 * this.index, 10, 100 - 15 * this.index)
        scene.add(newCube)
        this.index++
    },
    color: 0xffffff
}
ctrl.add(ctrlObj, "addCube")
ctrl.addColor(ctrlObj, "color")
ctrl.add(ctrlObj, "cameraNear", 0, 50).onChange((e) => {
    camera.near = e;
    camera.updateProjectionMatrix();
})
ctrl.add(ctrlObj, "cameraFar", 50, 1000).onChange((e) => {
    camera.far = e;
    camera.updateProjectionMatrix();
})
//创建一个物体
const cubeGeometry = new THREE.BoxGeometry(8, 8, 8);
const cubeMaterial = new THREE.MeshLambertMaterial({ color: 0xde4406 });
const cube = new THREE.Mesh(cubeGeometry, cubeMaterial);
cube.position.set(15, 10, 20)
cube.castShadow = true;

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

scene.add(axesHelper, ambientLight, directionalLight);
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
  