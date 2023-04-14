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
import { GLTFLoader } from 'three/examples/jsm/loaders/GLTFLoader.js'
import { RectAreaLightUniformsLib } from 'three/examples/jsm/lights/RectAreaLightUniformsLib'
import { RectAreaLightHelper } from 'three/examples/jsm/helpers/RectAreaLightHelper'
const scene = new THREE.Scene();
const camera = new THREE.PerspectiveCamera(75, window.innerWidth / window.innerHeight, 0.1, 1000);
camera.position.set(55, 55, 30);
const renderer = new THREE.WebGLRenderer();
const axesHelper = new THREE.AxesHelper(200);
renderer.setSize(window.innerWidth, window.innerHeight);
renderer.shadowMap.enabled = true;
const showThree: any = document.getElementsByClassName("threeArea");
const hemisphereLight = new THREE.HemisphereLight(0xffffff, 0xffffff, 1)
hemisphereLight.castShadow = true;
RectAreaLightUniformsLib.init();
const rectLight = new THREE.RectAreaLight(0x0000ff, 3, 30, 30);
rectLight.position.set(50, 50, 0);
rectLight.lookAt(0, 0, 0)
const rectLightHelper = new RectAreaLightHelper(rectLight);

const publicPath = process.env.BASE_URL
const ctrlObj = {
    skyLightColor: 0xffffff,
    groundLightColor: 0xffffff,
    hemisphereLightIntensity: 3,
    hemisphereLightVisible: true,
    rectColor: 0xffffff,
    rectVisible: true,
    rectIntensity: 3,
    rectWidth: 30,
    rectHeight: 30
}
const ctrl = new dat.GUI();
const hemisphereFolder = ctrl.addFolder("hemisphereLight")
hemisphereFolder.addColor(ctrlObj, "skyLightColor").onChange((e) => {
    hemisphereLight.color = new THREE.Color(e);
})
hemisphereFolder.addColor(ctrlObj, "groundLightColor").onChange((e) => {
    hemisphereLight.groundColor = new THREE.Color(e);
})
hemisphereFolder.add(ctrlObj, "hemisphereLightVisible").onChange((e) => {
    hemisphereLight.visible = e;
})
hemisphereFolder.add(ctrlObj, "hemisphereLightIntensity", 0.1, 1.5).onChange((e) => {
    hemisphereLight.intensity = e;
})
const rectFolder = ctrl.addFolder("rectAreaLight")
rectFolder.addColor(ctrlObj, "rectColor").onChange((e) => {
    rectLight.color = new THREE.Color(e);
})
rectFolder.add(ctrlObj, "rectIntensity", 1, 10).onChange((e) => {
    rectLight.intensity = e;
})
rectFolder.add(ctrlObj, "rectVisible").onChange((e) => {
    rectLight.visible = e;
})
rectFolder.add(ctrlObj, "rectWidth", 30, 100).onChange((e) => {
    rectLight.width = e;
})
rectFolder.add(ctrlObj, "rectHeight", 30, 100).onChange((e) => {
    rectLight.height = e;
})
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
const planeGeometry = new THREE.PlaneGeometry(300, 300)
const planeMaterial = new THREE.MeshStandardMaterial({ color: 0xffffff, roughness: 0.2, metal: 0 })
const plane = new THREE.Mesh(planeGeometry, planeMaterial)
plane.rotation.x = -0.5 * Math.PI;
plane.position.set(20, 0, 0);
plane.receiveShadow = true;

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

scene.add(cube, axesHelper, plane, hemisphereLight, rectLight, rectLightHelper);
//创建轨道控制器
const controls = new OrbitControls(camera, renderer.domElement);
const renderRequest = () => {
    stats.update();
    // pointLight.color = ctrlObj.pointColor;
    // pointLight.intensity = ctrlObj.pointIntensity;
    // pointLight.distance = ctrlObj.pointRadius;
    renderer.render(scene, camera);
    requestAnimationFrame(renderRequest)
}
renderRequest();
const formatTooltip = (val: number) => {
    return val / 100
}
const loader = new GLTFLoader();

const loadModel = () => {
    console.log('在这加载模型');
    loader.load(`${publicPath}test.gltf`, function (gltf) {
        gltf.scene.scale.set(3, 3, 3)
        gltf.scene.traverse((obj) => {
            for (let k in obj.children) {
                obj.children[k].castShadow = true;
                obj.children[k].receiveShadow = true;
            }
        })
        scene.add(gltf.scene);
    }, undefined, function (error) {
        console.error("err", error);
    });
}
loadModel();

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
  