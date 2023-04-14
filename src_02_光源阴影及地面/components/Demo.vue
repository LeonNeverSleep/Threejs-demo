<template>
    <div class="container">
        <div class="slider-demo-block">
            <span class="demonstration">相机高度</span>
            <el-slider v-model="cameraHeight" size="small" />
        </div>
        <div class="threeArea"></div>
    </div>
</template>
  
<script lang="ts" setup>
import { onMounted, ref } from 'vue'
import * as THREE from "three"
import { OrbitControls } from 'three/examples/jsm/controls/OrbitControls'
let cameraHeight = ref(20);
let renderWidth = ref(800);
let renderHeight = ref(400);
const scene = new THREE.Scene();
const camera = new THREE.PerspectiveCamera(75, window.innerWidth / window.innerHeight, 0.1, 1000);
const renderer = new THREE.WebGLRenderer();
const axesHelper = new THREE.AxesHelper(50);
renderer.setSize(renderWidth.value, renderHeight.value);
renderer.shadowMap.enabled = true;
const showThree: any = document.getElementsByClassName("threeArea");


onMounted(() => {
    showThree[0].appendChild(renderer.domElement);
})
//创建一个物体
const cubeGeometry = new THREE.BoxGeometry(8, 8, 8);
const cubeMaterial = new THREE.MeshLambertMaterial({ color: 0xffff00 });
const cube = new THREE.Mesh(cubeGeometry, cubeMaterial);
cube.position.set(4, 10, 20)
cube.castShadow = true;
const planeGeometry = new THREE.PlaneGeometry(100, 100)
const planeMaterial = new THREE.MeshLambertMaterial({ color: 0xcccccc })
const plane = new THREE.Mesh(planeGeometry, planeMaterial)
plane.rotation.x = -0.5 * Math.PI;
plane.position.set(20, 0, 0);
plane.receiveShadow = true;
const ambientLight = new THREE.AmbientLight(0xAAAAAA);
const spotLight = new THREE.SpotLight();
spotLight.position.set(-60, 40, -65);
spotLight.castShadow = true;
spotLight.shadow.mapSize = new THREE.Vector2(1024, 1024);
spotLight.shadow.camera.far = 130;
spotLight.shadow.camera.near = 40;


scene.add(cube, axesHelper, plane, ambientLight, spotLight);
//创建轨道控制器
const controls = new OrbitControls(camera, renderer.domElement);
renderer.render(scene, camera);
const renderRequest = () => {
    camera.position.z = cameraHeight.value
    renderer.render(scene, camera);
    requestAnimationFrame(renderRequest)
}
renderRequest();
const formatTooltip = (val: number) => {
    return val / 100
}
</script>
<style scoped>
.threeArea {
    display: flex;
    justify-content: center;
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
  