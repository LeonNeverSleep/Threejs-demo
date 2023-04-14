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
import { FontLoader } from 'three/examples/jsm/loaders/FontLoader'
import { TextGeometry } from 'three/examples/jsm/geometries/TextGeometry'
const scene = new THREE.Scene();
const camera = new THREE.PerspectiveCamera(75, window.innerWidth / window.innerHeight, 0.1, 1000);
camera.position.set(100, 50, 100);
const renderer = new THREE.WebGLRenderer();
const axesHelper = new THREE.AxesHelper(200);
renderer.setSize(window.innerWidth, window.innerHeight);
renderer.shadowMap.enabled = true;
const showThree: any = document.getElementsByClassName("threeArea");
const ctrl = new dat.GUI();
const path = process.env.BASE_URL
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
const frontDirectionalLight = new THREE.DirectionalLight(0xffffff, 2);
frontDirectionalLight.castShadow = true;
frontDirectionalLight.shadow.mapSize.width = 2048;
frontDirectionalLight.shadow.mapSize.height = 2048;
frontDirectionalLight.position.set(100, 100, 100)
const backDirectionalLight = new THREE.DirectionalLight(0xffffff, 2);
backDirectionalLight.castShadow = true;
backDirectionalLight.shadow.mapSize.width = 2048;
backDirectionalLight.shadow.mapSize.height = 2048;
backDirectionalLight.position.set(-100, 100, -100)
scene.add(frontDirectionalLight, backDirectionalLight)
let fontGeo, fontMat, fontObj, gfont
const loader = new FontLoader();
loader.load(`${path}helvetiker_regular.typeface.json`, (font) => {
    gfont = font
    fontGeo = new TextGeometry("SHRC", {
        font: gfont,
        size: 10,
        height: 30,
        curveSegments: 4,
        bevelEnabled: true,
        bevelThickness: 2,
        bevelSize: 1,
        bevelSegments: 4
    })
    fontMat = [
        new THREE.MeshPhongMaterial({ color: 0xcccccc, flatShading: true }),
        new THREE.MeshPhongMaterial({ color: 0xcccccc })
    ]
    fontObj = new THREE.Mesh(fontGeo, fontMat)
    fontObj.castShadow = true
    fontObj.position.set(20, 20, 0)
    scene.add(fontObj)
})
const ctrlObj = {
    font: gfont,
    size: 10,
    height: 30,
    curveSegments: 4,
    bevelEnabled: true,
    bevelThickness: 2,
    bevelSize: 1,
    bevelSegments: 4
}
const createText = () => {
    scene.remove(fontObj)
    fontGeo = new TextGeometry("SHRC", {
        font: gfont,
        size: ctrlObj.size,
        height: ctrlObj.height,
        curveSegments: ctrlObj.curveSegments,
        bevelEnabled: ctrlObj.bevelEnabled,
        bevelThickness: ctrlObj.bevelThickness,
        bevelSize: ctrlObj.bevelSize,
        bevelSegments: ctrlObj.bevelSegments
    })
    fontMat = [
        new THREE.MeshPhongMaterial({ color: 0xcccccc, flatShading: true }),
        new THREE.MeshPhongMaterial({ color: 0xcccccc })
    ]
    fontObj = new THREE.Mesh(fontGeo, fontMat)
    fontObj.castShadow = true
    fontObj.position.set(20, 20, 0)
    scene.add(fontObj)

}
ctrl.add(ctrlObj, "size", 0, 100).onChange(() => {
    createText();
})
ctrl.add(ctrlObj, "height", 0, 100).onChange(() => {
    createText();
})
ctrl.add(ctrlObj, "curveSegments", 0, 100).step(1).onChange(() => {
    createText();
})
ctrl.add(ctrlObj, "bevelEnabled").onChange(() => {
    createText();
})
ctrl.add(ctrlObj, "bevelThickness", 0, 10).onChange(() => {
    createText();
})
ctrl.add(ctrlObj, "bevelSize", 0, 1).onChange(() => {
    createText();
})
ctrl.add(ctrlObj, "bevelSegments", 0, 100).step(1).onChange(() => {
    createText();
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
  