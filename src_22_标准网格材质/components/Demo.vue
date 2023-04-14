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
import { OBJLoader } from 'three/examples/jsm/loaders/OBJLoader'
const scene = new THREE.Scene();
const camera = new THREE.PerspectiveCamera(75, window.innerWidth / window.innerHeight, 0.1, 1000);
camera.position.set(120, 60, -120);
const renderer = new THREE.WebGLRenderer();
const axesHelper = new THREE.AxesHelper(200);
renderer.setSize(window.innerWidth, window.innerHeight);
renderer.shadowMap.enabled = true;
const showThree: any = document.getElementsByClassName("threeArea");
const ctrl = new dat.GUI();
const path = process.env.BASE_URL;
onMounted(() => {
    showThree[0].appendChild(renderer.domElement);
})
const carMaterial = new THREE.MeshStandardMaterial({ color: 0x7777ff })
const setMaterialGroup = (material, group) => {
    if (group instanceof THREE.Mesh) {
        group.material = material
    } else if (group instanceof THREE.Group) {
        group.children.forEach(child => {
            setMaterialGroup(material, child)
        });
    }
}
const loadModel = (mat) => {
    const loader = new OBJLoader();
    let mesh = null;
    let p = new Promise((resolve, reject) => {
        loader.load(`${path}carModel/Lamborghini_Countach_LP800.obj`, (loadedMesh) => {
            mesh = loadedMesh
            if (mat) {
                setMaterialGroup(mat, mesh);
            }
            resolve(mesh)
        })
    })
    return p;
}
loadModel(carMaterial).then((item) => {
    item.scale.set(15, 15, 15)
    item.position.set(100, 0, 0)
    scene.add(item)
})
const props = {
    color: carMaterial.color.getStyle(),
    emissive: carMaterial.emissive.getStyle()
}
const standardMaterialFolder = ctrl.addFolder("standardMaterial")
standardMaterialFolder.addColor(props, "color").onChange((e) => {
    carMaterial.color.setStyle(e)
})
standardMaterialFolder.addColor(props, "emissive").onChange((e) => {
    carMaterial.emissive = new THREE.Color(e)
})
standardMaterialFolder.add(carMaterial, "metalness", 0, 1, 0.01)
standardMaterialFolder.add(carMaterial, "roughness", 0, 1, 0.01)
standardMaterialFolder.add(carMaterial, "wireframe")
const planeGeo = new THREE.PlaneGeometry(300, 300)
const planeMat = new THREE.MeshLambertMaterial({ color: 0xcccccc })
const plane = new THREE.Mesh(planeGeo, planeMat)
plane.rotation.x = -0.5 * Math.PI
plane.position.set(0, -20, 0)
const spotLight = new THREE.SpotLight(0xffffff, 0.6)
spotLight.position.set(50, 100, -50)
spotLight.castShadow = true;
plane.receiveShadow = true;
scene.add(plane, spotLight)
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

scene.add(axesHelper, ambientLight);
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
  