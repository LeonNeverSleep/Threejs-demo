<template>
    <div class="container">
        <div class="slider-demo-block">
            <span class="demonstration">相机高度</span>
            <el-slider v-model="cameraHeight" size="small" />
            <span class="demonstration">比例</span>
            <el-select v-model="selectValue" class="m-2" placeholder="Select" size="small">
                <el-option v-for="item in options" :key="item.value" :label="item.label" :value="item.value" />
            </el-select>
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
const axesHelper = new THREE.AxesHelper(15);
renderer.setSize(renderWidth.value, renderHeight.value);
const showThree: any = document.getElementsByClassName("threeArea");
let selectValue = ref('1')
const options = ref([
    {
        value: "1",
        label: '1',
    },
    {
        value: "2",
        label: '2',
    },
    {
        value: "3",
        label: '3',
    },
    {
        value: "4",
        label: '4',
    },
    {
        value: "5",
        label: '5',
    },
])
onMounted(() => {
    showThree[0].appendChild(renderer.domElement);
})
//创建一个物体
const cubeGeometry = new THREE.BoxGeometry(1, 1, 1);
const cubeMaterial = new THREE.MeshBasicMaterial({ color: 0xffff00 });
const cube = new THREE.Mesh(cubeGeometry, cubeMaterial);
scene.add(cube, axesHelper);
//创建轨道控制器
const controls = new OrbitControls(camera, renderer.domElement);
renderer.render(scene, camera);
console.log("kk", renderer.domElement);
const clock = new THREE.Clock();
const renderRequest = () => {
    let time = clock.getElapsedTime();
    let t = time % 5;
    console.log("我是time", time);
    console.log("我是t", t);
    cube.rotation.x += 0.01;
    cube.rotation.y += 0.01;
    cube.position.x = t * 1;
    cube.position.y = t * 1;
    renderer.setSize(renderWidth.value, renderHeight.value);
    camera.position.z = cameraHeight.value / Number(selectValue.value);
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
  