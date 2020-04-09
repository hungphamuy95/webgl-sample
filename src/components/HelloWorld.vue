<template>
  <div id="hello"></div>
</template>

<script>
/*eslint-disable */
import * as THREE from 'three';

export default {
  name: 'HelloWorld',
  data(){
    return{
      Colors: {
      red:0xf25346,
      white:0xd8d0d1,
      pink:0xF5986E,
      brown:0x59332e,
      brownDark:0x23190f,
      blue:0x68c3c0,
    },
    scene:{},
    camera:{}, 
    fieldOfView: {}, 
    aspectRatio: {}, 
    nearPlane:{}, 
    farPlane:{},
    renderer:{}, 
    container:{},
    HEIGHT: {},
    WIDTH: {},
    ambientLight:{}, 
    hemisphereLight:{}, 
    shadowLight: {}
    }
  },
  props: {
  },
  mounted(){
    this.init();
  },
  methods:{
    createScene(){
      this.HEIGHT = window.innerHeight;
      this.WIDTH = window.innerWidth;
      this.scene = new THREE.Scene();
      this.aspectRatio = this.WIDTH/this.HEIGHT;
      this.fieldOfView = 60;
      this.nearPlane = 1;
      this.farPlane = 10000;
      this.camera = new THREE.PerspectiveCamera(
        this.fieldOfView,
        this.aspectRatio,
        this.nearPlane,
        this.farPlane
      );
      this.scene.fog = new THREE.Fog(0xf7d9aa, 100, 950);
      this.camera.position.x = 0;
      this.camera.position.y = 200;
      this.camera.position.z = 100;

      this.renderer = new THREE.WebGLRenderer({alpha: true, antialias: true});
      this.renderer.setSize(this.WIDTH, this.HEIGHT);
      this.renderer.shadowMap.enabled = true;
      this.container = document.getElementById("hello");
      this.container.appendChild(this.renderer.domElement)
      window.addEventListener('resize',this.handleWindowResize(), false)
    },
    handleWindowResize(){
      this.HEIGHT = window.innerHeight;
      this.WIDTH = window.innerWidth;
      this.renderer.setSize(this.WIDTH, this.HEIGHT);
      this.camera.aspect = this.WIDTH/this.HEIGHT;
      this.camera.updateProjectionMatrix();
    },
    createLights(){
      this.hemisphereLight = new THREE.HemisphereLight(0xffffff, .9);
      this.ambientLight = new THREE.AmbientLight(0xdc8874, .5);
      this.shadowLight = new THREE.DirectionalLight(0xffffff, .9);
      this.shadowLight.castShadow = true;
      this.shadowLight.shadow.camera.left = -400;
      this.shadowLight.shadow.camera.right = 400;
      this.shadowLight.shadow.camera.top = 400;
      this.shadowLight.shadow.camera.bottom = -400;
      this.shadowLight.shadow.camera.near = 1;
      this.shadowLight.shadow.camera.far = 1000;
      this.shadowLight.shadow.mapSize.width = 2048;
      this.shadowLight.shadow.mapSize.height = 2048;
      this.scene.add(this.hemisphereLight);
      this.scene.add(this.ambientLight);
      this.scene.add(this.shadowLight);
    },
    createSampleObject(){
      const geometry = new THREE.BoxBufferGeometry( 1, 1, 1 );
      const material = new THREE.MeshBasicMaterial( {color: 0x57e5ff} );
      const cube = new THREE.Mesh( geometry, material );
      debugger;
      this.scene.addObject( cube );
    },
    init(){
      this.createScene();
      this.createLights();
      this.createSampleObject();
    }
  }
}
</script>

<!-- Add "scoped" attribute to limit CSS to this component only -->
<style scoped>
</style>
