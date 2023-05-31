<template>
  <div>
<!--    <div id="bottomLeftChart" style="width:11.25rem;height:6.25rem;"></div>-->
    <div ref="container" style="width:11.25rem;height:6.25rem;"></div>
  </div>
</template>

<script>
import echartMixins from "@/utils/resizeMixins";
import * as THREE from 'three'
import { OrbitControls } from 'three/examples/jsm/controls/OrbitControls.js';
import { GLTFLoader } from "three/examples/jsm/loaders/GLTFLoader"

export default {
  data() {
    return {
      cont: null,
      camera: null,
      scene: null,
      mesh: null,
      renderer: null,
    };
  },
  mixins: [echartMixins],
  mounted() {
    this.cont = this.$refs.container;
    this.init();
    this.animate();
    window.onresize = ()=>{
      this.camera.aspect = this.cont.clientWidth / this.cont.clientHeight;
      this.camera.updateProjectionMatrix();
      this.renderer.setSize( this.cont.clientWidth, this.cont.clientHeight, true);
    }
  },
  methods: {
    init(){
      this.scene = new THREE.Scene();

      //坐标设置
      // let axes = new THREE.AxesHelper(5);//5表示xyz轴的长度，红:x,绿:y,蓝:z
      // this.scene.add(axes);

      //gltf模型加载
      let self = this;
      const loader = new GLTFLoader();
      loader.load("/static/konglong/scene.gltf", function(gltf) {
        self.isLoading = false;//关闭载入中效果
        self.mesh = gltf.scene;
        self.mesh.scale.set(0.4, 0.4, 0.4);//设置模型缩放比例
        self.mesh.position.set(0, 0, 0);//设置位置
        self.scene.add(self.mesh); // 将模型引入three、
        self.animate();
      });


      //相机设置
      this.camera = new THREE.PerspectiveCamera(45, this.cont.clientWidth / this.cont.clientHeight, 0.1, 1000);
      this.camera.position.set(2, 2, 2); //设置相机的位置,距离模型远点x/y/z轴坐标
      this.camera.lookAt(new THREE.Vector3(0,0,0)); //设置相机拍摄的方向，看向原点位置
      this.renderer = new THREE.WebGLRenderer({ antialias: true });
      this.renderer.setSize(this.cont.clientWidth, this.cont.clientHeight, true);//设置渲染区域尺寸
      this.renderer.setClearColor(0xb9d3ff, 1); //设置背景颜色
      this.cont.appendChild(this.renderer.domElement);

      //光源设置
      let ambientLight = new THREE.AmbientLight(0xffffff); //设置环境光
      this.scene.add(ambientLight); //将环境光添加到场景中
      let pointLight = new THREE.PointLight(0xffffff, 1, 0);
      pointLight.position.set(200, 200, 200); //设置点光源位置
      this.scene.add(pointLight); //将点光源添加至场景

      new OrbitControls(this.camera, this.renderer.domElement);
    },
    animate(){
      if (this.mesh) {
        requestAnimationFrame(this.animate);
        // this.mesh.rotation.x += 0.01;
        this.mesh.rotation.y += 0.004;
        this.renderer.render(this.scene, this.camera);
      }
    }
  },
  destroyed() {
    window.onresize = null;
  }
};
</script>

<style lang="scss" scoped>
</style>