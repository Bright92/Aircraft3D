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
// import { OBJLoader,MTLLoader } from 'three-obj-mtl-loader'
import { OBJLoader } from 'three/examples/jsm/loaders/OBJLoader.js'
import { MTLLoader } from 'three/examples/jsm/loaders/MTLLoader.js'

export default {
  data() {
    return {
      cont: null,
      camera: null,
      scene: null,
      mesh: null,
      renderer: null,
      controls: null
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

      //obj模型加载
      let that = this
      let objLoader = new OBJLoader()
      let mtlLoader = new MTLLoader()

      mtlLoader.load('/static/AJ1/AJ1.mtl', function (materials) {
            materials.preload();
            objLoader.setMaterials(materials)
            objLoader.load('/static/AJ1/AJ1.obj', function (obj) {
                  that.mesh = obj;
                  obj.position.set(0, 0, 0)
                  obj.scale.set(20, 20, 20)
                  that.scene.add(obj)
                  that.animate()
                },
                //   called while loading is progressing
                function (xhr) {
                  console.log((xhr.loaded / xhr.total) * 100 + '% loaded')
                },
                //   called when loading has errors
                function (error) {
                  console.log(error+'An error happened')
                }
      )})

      //相机设置
      this.camera = new THREE.PerspectiveCamera(45, this.cont.clientWidth / this.cont.clientHeight, 0.1, 1000);
      this.camera.position.set(200, 200, 200); //设置相机的位置,距离模型远点x/y/z轴坐标
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

      this.controls = new OrbitControls(this.camera, this.renderer.domElement);
    },
    animate(){
      if (this.mesh) {
        requestAnimationFrame(this.animate);
        this.mesh.rotation.y += 0.004;
        this.renderer.render(this.scene, this.camera);
        // this.controls.update();
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