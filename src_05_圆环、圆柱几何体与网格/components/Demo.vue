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


onMounted(() => {
    showThree[0].appendChild(renderer.domElement);
})
//创建一个物体
const cubeGeometry = new THREE.BoxGeometry(8, 8, 8);
const cubeMaterial = new THREE.MeshLambertMaterial({ color: 0xffff00 });
const cube = new THREE.Mesh(cubeGeometry, cubeMaterial);
cube.position.set(4, 10, 20)
cube.castShadow = true;
// const cube2Geometry = new THREE.BoxGeometry(8, 8, 8);
// const cube2Material = new THREE.MeshLambertMaterial({ color: 0x0000ff });
// const cube2 = new THREE.Mesh(cube2Geometry, cube2Material);
// cube2.position.set(5, 10, 20)
// cube2.castShadow = true;
let torusGeometry = new THREE.TorusGeometry(10, 3, 15, 20);
const torusMaterial = new THREE.MeshLambertMaterial({ color: 0xff2288 });
let torus = new THREE.Mesh(torusGeometry, torusMaterial);
let cylinderGeometry = new THREE.CylinderGeometry(5, 6, 16, 33, 10, false, 2, Math.PI * 2);
const cylinderMaterial = new THREE.MeshLambertMaterial({ color: 0x2288ff })
let cylinder = new THREE.Mesh(cylinderGeometry, cylinderMaterial);
cylinder.castShadow = true;
cylinder.position.set(20, 20, 0)
torus.castShadow = true;
torus.position.set(-30, 20, 35);
scene.add(torus, cylinder);
console.log("torus", torus);
const ctrlObj = {
    // 圆环属性
    torusRadius: 10,
    torusTube: 3,
    radialSegments: 15,
    tubularSegments: 100,
    torusArc: Math.PI * 2,
    // 圆柱属性
    radiusTop: 5,
    radiusBottom: 5,
    cylinderHeight: 10,
    cylinderRadialSegments: 32,
    cylinderHeightSegments: 2,
    openEnded: false,
    thetaStart: 0,
    thetaLength: Math.PI * 2
}

const ctrl = new dat.GUI();
ctrl.add(ctrlObj, "torusRadius", 1, 20).onChange((e) => {
    scene.remove(torus)
    torusGeometry = new THREE.TorusGeometry(ctrlObj.torusRadius, ctrlObj.torusTube, ctrlObj.radialSegments, ctrlObj.tubularSegments, ctrlObj.torusArc)
    torus = new THREE.Mesh(torusGeometry, torusMaterial)
    scene.add(torus)
    torus.castShadow = true;
    torus.position.set(-30, 20, 35);
});
ctrl.add(ctrlObj, "torusTube", 0.1, 10).onChange((e) => {
    scene.remove(torus)
    torusGeometry = new THREE.TorusGeometry(ctrlObj.torusRadius, ctrlObj.torusTube, ctrlObj.radialSegments, ctrlObj.tubularSegments, ctrlObj.torusArc)
    torus = new THREE.Mesh(torusGeometry, torusMaterial)
    scene.add(torus)
    torus.castShadow = true;
    torus.position.set(-30, 20, 35);
});
ctrl.add(ctrlObj, "radialSegments", 2, 30).onChange((e) => {
    scene.remove(torus)
    torusGeometry = new THREE.TorusGeometry(ctrlObj.torusRadius, ctrlObj.torusTube, ctrlObj.radialSegments, ctrlObj.tubularSegments, ctrlObj.torusArc)
    torus = new THREE.Mesh(torusGeometry, torusMaterial)
    scene.add(torus)
    torus.castShadow = true;
    torus.position.set(-30, 20, 35);
})
ctrl.add(ctrlObj, "tubularSegments", 3, 40).onChange((e) => {
    scene.remove(torus)
    torusGeometry = new THREE.TorusGeometry(ctrlObj.torusRadius, ctrlObj.torusTube, ctrlObj.radialSegments, ctrlObj.tubularSegments, ctrlObj.torusArc)
    torus = new THREE.Mesh(torusGeometry, torusMaterial)
    scene.add(torus)
    torus.castShadow = true;
    torus.position.set(-30, 20, 35);
})
ctrl.add(ctrlObj, "torusArc", 0.1, Math.PI * 2).onChange((e) => {
    scene.remove(torus)
    torusGeometry = new THREE.TorusGeometry(ctrlObj.torusRadius, ctrlObj.torusTube, ctrlObj.radialSegments, ctrlObj.tubularSegments, ctrlObj.torusArc)
    torus = new THREE.Mesh(torusGeometry, torusMaterial)
    scene.add(torus)
    torus.castShadow = true;
    torus.position.set(-30, 20, 35);
})
ctrl.add(ctrlObj, "radiusTop", 0, 30).onChange((e) => {
    console.log('rrr');
    scene.remove(cylinder)
    cylinderGeometry = new THREE.CylinderGeometry(ctrlObj.radiusTop, ctrlObj.radiusBottom, ctrlObj.cylinderHeight, ctrlObj.cylinderRadialSegments, ctrlObj.cylinderHeightSegments, ctrlObj.openEnded, ctrlObj.thetaStart, ctrlObj.thetaLength)
    cylinder = new THREE.Mesh(cylinderGeometry, cylinderMaterial);
    cylinder.castShadow = true;
    cylinder.position.set(20, 20, 0)
    scene.add(cylinder)
})
ctrl.add(ctrlObj, "radiusBottom", 0, 30).onChange((e) => {
    scene.remove(cylinder)
    cylinderGeometry = new THREE.CylinderGeometry(ctrlObj.radiusTop, ctrlObj.radiusBottom, ctrlObj.cylinderHeight, ctrlObj.cylinderRadialSegments, ctrlObj.cylinderHeightSegments, ctrlObj.openEnded, ctrlObj.thetaStart, ctrlObj.thetaLength)
    cylinder = new THREE.Mesh(cylinderGeometry, cylinderMaterial);
    cylinder.castShadow = true;
    cylinder.position.set(20, 20, 0)
    scene.add(cylinder)
})
ctrl.add(ctrlObj, "cylinderHeight", 1, 50).onChange((e) => {
    scene.remove(cylinder)
    cylinderGeometry = new THREE.CylinderGeometry(ctrlObj.radiusTop, ctrlObj.radiusBottom, ctrlObj.cylinderHeight, ctrlObj.cylinderRadialSegments, ctrlObj.cylinderHeightSegments, ctrlObj.openEnded, ctrlObj.thetaStart, ctrlObj.thetaLength)
    cylinder = new THREE.Mesh(cylinderGeometry, cylinderMaterial);
    cylinder.castShadow = true;
    cylinder.position.set(20, 20, 0)
    scene.add(cylinder)
})
ctrl.add(ctrlObj, "cylinderRadialSegments", 3, 64).onChange((e) => {
    scene.remove(cylinder)
    cylinderGeometry = new THREE.CylinderGeometry(ctrlObj.radiusTop, ctrlObj.radiusBottom, ctrlObj.cylinderHeight, ctrlObj.cylinderRadialSegments, ctrlObj.cylinderHeightSegments, ctrlObj.openEnded, ctrlObj.thetaStart, ctrlObj.thetaLength)
    cylinder = new THREE.Mesh(cylinderGeometry, cylinderMaterial);
    cylinder.castShadow = true;
    cylinder.position.set(20, 20, 0)
    scene.add(cylinder)
})
ctrl.add(ctrlObj, "cylinderHeightSegments", 1, 64).onChange((e) => {
    scene.remove(cylinder)
    cylinderGeometry = new THREE.CylinderGeometry(ctrlObj.radiusTop, ctrlObj.radiusBottom, ctrlObj.cylinderHeight, ctrlObj.cylinderRadialSegments, ctrlObj.cylinderHeightSegments, ctrlObj.openEnded, ctrlObj.thetaStart, ctrlObj.thetaLength)
    cylinder = new THREE.Mesh(cylinderGeometry, cylinderMaterial);
    cylinder.castShadow = true;
    cylinder.position.set(20, 20, 0)
    scene.add(cylinder)
})
ctrl.add(ctrlObj, "openEnded").onChange((e) => {
    scene.remove(cylinder)
    cylinderGeometry = new THREE.CylinderGeometry(ctrlObj.radiusTop, ctrlObj.radiusBottom, ctrlObj.cylinderHeight, ctrlObj.cylinderRadialSegments, ctrlObj.cylinderHeightSegments, ctrlObj.openEnded, ctrlObj.thetaStart, ctrlObj.thetaLength)
    cylinder = new THREE.Mesh(cylinderGeometry, cylinderMaterial);
    cylinder.castShadow = true;
    cylinder.position.set(20, 20, 0)
    scene.add(cylinder)
})
ctrl.add(ctrlObj, "thetaStart", 0, Math.PI * 2).onChange((e) => {
    scene.remove(cylinder)
    cylinderGeometry = new THREE.CylinderGeometry(ctrlObj.radiusTop, ctrlObj.radiusBottom, ctrlObj.cylinderHeight, ctrlObj.cylinderRadialSegments, ctrlObj.cylinderHeightSegments, ctrlObj.openEnded, ctrlObj.thetaStart, ctrlObj.thetaLength)
    cylinder = new THREE.Mesh(cylinderGeometry, cylinderMaterial);
    cylinder.castShadow = true;
    cylinder.position.set(20, 20, 0)
    scene.add(cylinder)
})
ctrl.add(ctrlObj, "thetaLength", 0, Math.PI * 2).onChange((e) => {
    scene.remove(cylinder)
    cylinderGeometry = new THREE.CylinderGeometry(ctrlObj.radiusTop, ctrlObj.radiusBottom, ctrlObj.cylinderHeight, ctrlObj.cylinderRadialSegments, ctrlObj.cylinderHeightSegments, ctrlObj.openEnded, ctrlObj.thetaStart, ctrlObj.thetaLength)
    cylinder = new THREE.Mesh(cylinderGeometry, cylinderMaterial);
    cylinder.castShadow = true;
    cylinder.position.set(20, 20, 0)
    scene.add(cylinder)
})
// ctrl.add(ctrlObj2, "addNewCube");
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
console.log("scene", scene);

scene.add(cube, axesHelper, plane, ambientLight, spotLight);
//创建轨道控制器
const controls = new OrbitControls(camera, renderer.domElement);
// renderer.render(scene, camera);
// let gap = 0;
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
  