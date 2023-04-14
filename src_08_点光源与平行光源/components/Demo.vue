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

const scene = new THREE.Scene();
const camera = new THREE.PerspectiveCamera(75, window.innerWidth / window.innerHeight, 0.1, 1000);
camera.position.set(55, 55, 30);
const renderer = new THREE.WebGLRenderer();
const axesHelper = new THREE.AxesHelper(200);
renderer.setSize(window.innerWidth, window.innerHeight);
renderer.shadowMap.enabled = true;
const showThree: any = document.getElementsByClassName("threeArea");
const directionalLight = new THREE.DirectionalLight(0xffffff, 2);
directionalLight.castShadow = true;
directionalLight.shadow.mapSize.width = 2048;
directionalLight.shadow.mapSize.height = 2048;
directionalLight.position.set(100, 100, 100)
const directionalHelper = new THREE.DirectionalLightHelper(directionalLight)
scene.add(directionalHelper);
const publicPath = process.env.BASE_URL
const ctrlObj = {
    pointColor: 0xffffff,
    pointIntensity: 3,
    pointRadius: 60,
    pointDecay: 2,
    pointVisible: true,
    directColor: 0xffffff,
    directIntensity: 3,
    directVisible: true
}
const ctrl = new dat.GUI();
const pointLightFolder = ctrl.addFolder("pointLight")
pointLightFolder.addColor(ctrlObj, "pointColor").onChange((e) => {
    pointLight.color = new THREE.Color(e);
})
pointLightFolder.add(ctrlObj, "pointIntensity", 1, 10).onChange((e) => {
    pointLight.intensity = e;
});
pointLightFolder.add(ctrlObj, "pointRadius", 20, 100).onChange((e) => {
    pointLight.distance = e;
});
pointLightFolder.add(ctrlObj, "pointDecay", 1, 10).onChange((e) => {
    pointLight.decay = e;
});
pointLightFolder.add(ctrlObj, "pointVisible").onChange((e) => {
    pointLight.visible = e;
    sphere.visible = e;
});
const directionalLightFolder = ctrl.addFolder("directionalLight")
directionalLightFolder.addColor(ctrlObj, "directColor").onChange((e) => {
    directionalLight.color = new THREE.Color(e);
})
directionalLightFolder.add(ctrlObj, "directIntensity", 1, 10).onChange((e) => {
    directionalLight.intensity = e;
});
directionalLightFolder.add(ctrlObj, "directVisible").onChange((e) => {
    directionalLight.visible = e;
});
console.log("ttt", THREE);

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
const planeMaterial = new THREE.MeshLambertMaterial({ color: 0xcccccc })
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
const pointLight = new THREE.PointLight(0x00ff00, 3, 60)
pointLight.position.set(30, 30, 30)
const sphereGeometry = new THREE.SphereGeometry(0.3);
const sphereMaterial = new THREE.MeshBasicMaterial({ color: 0x00ff00 })
const sphere = new THREE.Mesh(sphereGeometry, sphereMaterial);
sphere.castShadow = true;
sphere.position.copy(pointLight.position);
scene.add(cube, axesHelper, plane, pointLight, sphere, directionalLight);
//创建轨道控制器
const controls = new OrbitControls(camera, renderer.domElement);
let gap = 0;
const renderRequest = () => {
    stats.update();
    gap += 0.01;
    pointLight.position.y = 6 + (20 * Math.abs(Math.cos(gap)))
    sphere.position.copy(pointLight.position);
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
            if (obj.isMesh) {
                obj.castShadow = true;
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
  