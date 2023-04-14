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
const logoShape = new THREE.Shape()
logoShape.moveTo(0, 0)
logoShape.lineTo(20, 10)
logoShape.lineTo(30, 20)
logoShape.lineTo(-10, 30)
logoShape.lineTo(50, 40)
const shapeGeo = new THREE.ShapeGeometry(logoShape)
const shapeMat = new THREE.MeshBasicMaterial({ color: 0xcccccc, side: THREE.DoubleSide })
const shape = new THREE.Mesh(shapeGeo, shapeMat)
scene.add(shape)
const extrudeSettings = {
    steps: 2,
    depth: 16,
    bevelEnabled: true,
    bevelThickness: 1,
    bevelSize: 1,
    bevelOffset: 0,
    bevelSegments: 1
}
let extrudeGeo = new THREE.ExtrudeGeometry(logoShape, extrudeSettings);
const extrudeMat = new THREE.MeshBasicMaterial({ color: 0xcccccc, wireframe: true, side: THREE.DoubleSide })
let extrude = new THREE.Mesh(extrudeGeo, extrudeMat)
scene.add(extrude)

ctrl.add(extrudeSettings, "steps", 1, 10).onChange(() => {
    scene.remove(extrude)
    extrudeGeo = new THREE.ExtrudeGeometry(logoShape, extrudeSettings)
    extrude = new THREE.Mesh(extrudeGeo, extrudeMat)
    scene.add(extrude)
})
ctrl.add(extrudeSettings, "depth", 1, 20).onChange(() => {
    scene.remove(extrude)
    extrudeGeo = new THREE.ExtrudeGeometry(logoShape, extrudeSettings)
    extrude = new THREE.Mesh(extrudeGeo, extrudeMat)
    scene.add(extrude)
})
ctrl.add(extrudeSettings, "bevelEnabled").onChange(() => {
    scene.remove(extrude)
    extrudeGeo = new THREE.ExtrudeGeometry(logoShape, extrudeSettings)
    extrude = new THREE.Mesh(extrudeGeo, extrudeMat)
    scene.add(extrude)
})
ctrl.add(extrudeSettings, "bevelThickness", 1, 5).onChange(() => {
    scene.remove(extrude)
    extrudeGeo = new THREE.ExtrudeGeometry(logoShape, extrudeSettings)
    extrude = new THREE.Mesh(extrudeGeo, extrudeMat)
    scene.add(extrude)
})
ctrl.add(extrudeSettings, "bevelSize", 0, 5).onChange(() => {
    scene.remove(extrude)
    extrudeGeo = new THREE.ExtrudeGeometry(logoShape, extrudeSettings)
    extrude = new THREE.Mesh(extrudeGeo, extrudeMat)
    scene.add(extrude)
})
ctrl.add(extrudeSettings, "bevelOffset", -4, 5).onChange(() => {
    scene.remove(extrude)
    extrudeGeo = new THREE.ExtrudeGeometry(logoShape, extrudeSettings)
    extrude = new THREE.Mesh(extrudeGeo, extrudeMat)
    scene.add(extrude)
})
ctrl.add(extrudeSettings, "bevelSegments", 1, 5).onChange(() => {
    scene.remove(extrude)
    extrudeGeo = new THREE.ExtrudeGeometry(logoShape, extrudeSettings)
    extrude = new THREE.Mesh(extrudeGeo, extrudeMat)
    scene.add(extrude)
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
  