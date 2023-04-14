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
import Color from 'element-plus/es/components/color-picker/src/utils/color';
let cameraHeight = ref(20);
const scene = new THREE.Scene();
// const camera = new THREE.PerspectiveCamera(75, window.innerWidth / window.innerHeight, 0.1, 1000);
let camera = new THREE.OrthographicCamera(window.innerWidth / - 16, window.innerWidth / 16, window.innerHeight / 16, window.innerHeight / - 16, -200, 500);
camera.position.set(55, 55, 30);
const renderer = new THREE.WebGLRenderer();
const axesHelper = new THREE.AxesHelper(200);
renderer.setSize(window.innerWidth, window.innerHeight);
renderer.shadowMap.enabled = true;
const showThree: any = document.getElementsByClassName("threeArea");
let controls = new OrbitControls(camera, renderer.domElement);

const ctrlObj = new function () {
    this.showText = "透视投影摄像机"
    this.changeCamera = function () {
        if (camera instanceof THREE.PerspectiveCamera) {
            camera = new THREE.OrthographicCamera(window.innerWidth / - 16, window.innerWidth / 16, window.innerHeight / 16, window.innerHeight / - 16, -200, 500);
            camera.position.set(-30, 40, 30);
            camera.lookAt(scene.position);
            controls = new OrbitControls(camera, renderer.domElement);
            this.showText = "正交投影摄像机"
        } else {
            camera = new THREE.PerspectiveCamera(75, window.innerWidth / window.innerHeight, 0.1, 1000)
            camera.position.set(-30, 40, 30);
            camera.lookAt(scene.position);
            controls = new OrbitControls(camera, renderer.domElement);
            this.showText = "透视投影摄像机"
        }
    }
}
const ctrl = new dat.GUI();
ctrl.add(ctrlObj, "showText").listen()
ctrl.add(ctrlObj, "changeCamera")
onMounted(() => {
    showThree[0].appendChild(renderer.domElement);
})
//创建一个物体
const cubeGeometry = new THREE.BoxGeometry(8, 8, 8);
const cubeMaterial = new THREE.MeshLambertMaterial({ color: 0xf80000 });
const cube = new THREE.Mesh(cubeGeometry, cubeMaterial);
cube.position.set(15, 10, 20)
cube.castShadow = true;

const planeGeometry = new THREE.PlaneGeometry(100, 100)
const planeMaterial = new THREE.MeshLambertMaterial({ color: 0xcccccc })
const plane = new THREE.Mesh(planeGeometry, planeMaterial)
plane.rotation.x = -0.5 * Math.PI;
plane.position.set(14, 0, 1);
plane.receiveShadow = true;
for (let i = 0; i < planeGeometry.parameters.height / 5; i++) {
    for (let j = 0; j < planeGeometry.parameters.height / 5; j++) {
        const floorGeometry = new THREE.BoxGeometry(4, 4, 4);
        const floorMaterial = new THREE.MeshLambertMaterial();
        floorMaterial.color = new THREE.Color(0, Math.random() * 0.25 + 0.5, 0)
        const floor = new THREE.Mesh(floorGeometry, floorMaterial);
        scene.add(floor)
        floor.position.x = -(planeGeometry.parameters.width / 2) + 15 + (i * 5)
        floor.position.y = 0
        floor.position.z = -(planeGeometry.parameters.height / 2) + 5 + (j * 5)
    }
}

const ambientLight = new THREE.AmbientLight(0xAAAAAA);
const spotLight = new THREE.SpotLight();
spotLight.position.set(-40, 40, -40);
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

scene.add(cube, axesHelper, plane, ambientLight, spotLight);
let gap = 0;
//创建轨道控制器
const renderRequest = () => {
    stats.update();
    gap += 0.01
    cube.position.x = 10 + (100 * (Math.sin(gap)));
    camera.lookAt(cube.position)
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
  