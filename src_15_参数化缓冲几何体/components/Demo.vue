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
import { ParametricGeometry } from 'three/examples/jsm/geometries/ParametricGeometry'
import { ParametricGeometries } from 'three/examples/jsm/geometries/ParametricGeometries'
import { useNetwork } from '@vueuse/core';
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
cube.position.set(30, 30, 30)
cube.castShadow = true;
const planeGeo = new THREE.PlaneGeometry(300, 300)
const planeMat = new THREE.MeshLambertMaterial({ color: 0xcccccc })
const plane = new THREE.Mesh(planeGeo, planeMat)
plane.rotation.x = -0.5 * Math.PI
plane.receiveShadow = true
scene.add(plane)
const directionalLight = new THREE.DirectionalLight(0xffffff, 2);
directionalLight.castShadow = true;
directionalLight.shadow.mapSize.width = 2048;
directionalLight.shadow.mapSize.height = 2048;
directionalLight.position.set(100, 100, 100)
scene.add(cube, directionalLight)
const kleinGeo = new ParametricGeometry(ParametricGeometries.klein, 80, 80)
const kleinMat = new THREE.MeshLambertMaterial({ color: 0xff0000 })
const klein = new THREE.Mesh(kleinGeo, kleinMat)
klein.position.set(0, 20, 0)
scene.add(klein)
const mobiusGeo = new ParametricGeometry(ParametricGeometries.mobius3d, 40, 40)
const mobiusMat = new THREE.MeshLambertMaterial({ color: 0x0000ff })
const mobius = new THREE.Mesh(mobiusGeo, mobiusMat)
mobius.position.set(30, 20, 0)
scene.add(mobius)
const customGeo = (u, v, target) => {
    let x, y, z;
    x = 20 * (u - 0.5)
    z = 20 * (v - 0.5)
    y = 2 * (Math.sin(x / 2) * Math.cos(z))
    target.set(x, y, z)
}
const seaGeo = new ParametricGeometry(customGeo, 80, 80)
const seaMat = new THREE.MeshPhongMaterial({
    specular: 0xaaaaff,
    color: 0x3366ff,
    shininess: 40,
    metal: true
})
seaMat.side = THREE.DoubleSide
const sea = new THREE.Mesh(seaGeo, seaMat)
sea.position.set(-20, 20, 0)
scene.add(sea)
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
  