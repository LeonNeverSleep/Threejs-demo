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
const ctrl = new dat.GUI();
const ctrlObj = {
    visible: true,
    transparent: false,
    opacity: 1,
    alphaTest: 1,
    side: "front",
    depthTest: true,
    depthWrite: true,
    wireframe: false,
    map: true,
    color: 0xde4406,
    reflectivity: 1,
    combine: "Multiply",
}
const path = process.env.BASE_URL;
onMounted(() => {
    showThree[0].appendChild(renderer.domElement);
})
const loader = new THREE.TextureLoader();
const bricks = loader.load(`${path}aaa.png`)

//创建一个物体
const cubeGeometry = new THREE.BoxGeometry(8, 8, 8);
const cubeMaterial = new THREE.MeshLambertMaterial({ color: 0xde4406 });
const cube = new THREE.Mesh(cubeGeometry, cubeMaterial);
cubeMaterial.map = bricks;
cube.position.set(15, 10, 20)
cube.castShadow = true;
const MaterialClass = ctrl.addFolder("Material")
MaterialClass.add(ctrlObj, "visible").onChange((e) => {
    cubeMaterial.visible = e;
})
MaterialClass.add(ctrlObj, "transparent").onChange((e) => {
    cubeMaterial.transparent = e;
})
MaterialClass.add(ctrlObj, "opacity", 0, 1).onChange((e) => {
    cubeMaterial.opacity = e;
})
MaterialClass.add(ctrlObj, "alphaTest", 0, 1).onChange((e) => {
    cubeMaterial.alphaTest = e;
})
MaterialClass.add(ctrlObj, "depthTest").onChange((e) => {
    cubeMaterial.depthTest = e;
})
MaterialClass.add(ctrlObj, "depthWrite").onChange((e) => {
    cubeMaterial.depthWrite = e;
})
MaterialClass.add(ctrlObj, "side", ["front", "back", "doubleSide"]).onChange((e) => {
    switch (e) {
        case "front":
            cubeMaterial.side = THREE.FrontSide
            break;
        case "back":
            cubeMaterial.side = THREE.BackSide
            break;
        case "doubleSide":
            cubeMaterial.side = THREE.DoubleSide
            break;

        default:
            break;
    }
})

const basicMaterial = ctrl.addFolder("MeshBasicMaterial")
basicMaterial.add(ctrlObj, "wireframe").onChange((e) => {
    cubeMaterial.wireframe = e;
})
basicMaterial.add(ctrlObj, "map").onChange((e) => {
    cubeMaterial.map = e ? bricks : null;
    cubeMaterial.needsUpdate = true;
})
basicMaterial.addColor(ctrlObj, "color").onChange((e) => {
    cubeMaterial.color = new THREE.Color(e)
})
basicMaterial.add(ctrlObj, "reflectivity", 0, 1).onChange((e) => {
    cubeMaterial.reflectivity = e;
})
basicMaterial.add(ctrlObj, "combine", ["Multiply", "Mix", "Add"]).onChange((e) => {
    switch (e) {
        case "Multiply":
            cubeMaterial.combine = THREE.MultiplyOperation
            break;
        case "Mix":
            cubeMaterial.combine = THREE.MixOperation
            break;
        case "Add":
            cubeMaterial.combine = THREE.AddOperation
            break;

        default:
            break;
    }
    cubeMaterial.needsUpdate = true;
})
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
const createEnvTexture = () => {
    const urls = [`${path}envTexture/px.jpg`,
    `${path}envTexture/nx.jpg`,
    `${path}envTexture/py.jpg`,
    `${path}envTexture/ny.jpg`,
    `${path}envTexture/pz.jpg`,
    `${path}envTexture/nz.jpg`
    ]
    const cubeEnvTxLoader = new THREE.CubeTextureLoader();
    const refCube = cubeEnvTxLoader.load(urls);
    return refCube
}
const cubeEnvTexture = createEnvTexture();
cubeMaterial.envMap = cubeEnvTexture;

scene.background = cubeEnvTexture;
scene.add(cube, axesHelper, ambientLight, spotLight);
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
  