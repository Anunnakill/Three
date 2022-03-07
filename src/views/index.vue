<script setup lang="ts">
import {
  Scene,
  Color,
  Vector2,
  AmbientLight,
  WebGLRenderer,
  DirectionalLight,
  PerspectiveCamera,
} from "three";

import { onMounted, ref } from "vue";

import { GLTFLoader } from "three/examples/jsm/loaders/GLTFLoader.js";

let dom: any = ref(null);

let scene: any, camera: any, renderer: any, loader, mesh: any;

const animate = () => {
  requestAnimationFrame(animate);
  renderer.render(scene, camera);
};

const mousemove = (evt: any) => {
  let mouse = new Vector2();
  let x = (evt.x / window.innerWidth) * 2 - 1;
  let y = -(-(evt.y / window.innerHeight) * 2 + 1);

  mouse.x = x;
  mouse.y = y;

  mesh.rotation.x = mouse.y;
  mesh.rotation.y = mouse.x;
};

const init = () => {
  scene = new Scene();
  scene.background = new Color(0x404040);

  renderer = new WebGLRenderer({ antialias: true, alpha: true });
  renderer.setSize(window.innerWidth, window.innerHeight);
  renderer.setPixelRatio(window.devicePixelRatio);
  dom.value.appendChild(renderer.domElement);

  let PR = window.innerWidth / window.innerHeight;
  camera = new PerspectiveCamera(45, PR);
  camera.position.set(0, 0, 200);

  let directionalLight = new DirectionalLight(0xffffff, 5);
  let ambientLight = new AmbientLight(0x404040, 5);
  scene.add(directionalLight);
  scene.add(ambientLight);

  loader = new GLTFLoader();

  loader.load("/src/assets/models/mask/scene.gltf", res => {
    mesh = res.scene;

    window.addEventListener("mousemove", mousemove, false);

    mesh.traverse(({ isMesh, geometry }: any) => {
      if (isMesh) geometry.center();
    });

    camera.lookAt(mesh.position);

    mesh.scale.set(1, 1, 1);

    scene.add(mesh);

    animate();
  });
};

onMounted(init);
</script>

<template>
  <div id="index" ref="dom" />
</template>

<style lang="scss" scoped>
#index {
  flex: 1;
  display: flex;
  flex-direction: column;
}
</style>
