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
    shadowLight: {},
    sea:{},
    plane:{},
    sky:{},
    airplane:{},
    mousePos: { x: 0, y: 0 }
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
      this.camera.position.y = 85;
      this.camera.position.z = 200;

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
      this.hemisphereLight = new THREE.HemisphereLight(0xffe6b3, .9);
      this.ambientLight = new THREE.AmbientLight(0xffe6b3, .5);
      this.shadowLight = new THREE.DirectionalLight(0xffe6b3, .9);
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
    airPlane: function(){
      this.mesh = new THREE.Object3D();
      
      // create the cabin
      const geomeCockpit = new THREE.BoxGeometry(60,50,50,1,1,1);
      const matCockpit = new THREE.MeshPhongMaterial({color:0xff604e, flatShading: THREE.FlatShading});
      const cockpit = new THREE.Mesh(geomeCockpit, matCockpit);
      cockpit.castShadow = true;
      cockpit.receiveShadow = true;
      this.mesh.add(cockpit);

      // create engine
      const geoEngine = new THREE.BoxGeometry(20,50,50,1,1,1);
      const matEngine = new THREE.MeshPhongMaterial({color:0xffffff, flatShading: THREE.FlatShading});
      const engine = new THREE.Mesh(geoEngine, matEngine);
      engine.position.x = 40;
      engine.castShadow = true;
      engine.receiveShadow = true;
      this.mesh.add(engine);
      

      // create tail plane
      const geomTailplane = new THREE.BoxGeometry(15,20,5,1,1,1);
      const matTailplane = new THREE.MeshPhongMaterial({color:0xff604e, flatShading: THREE.FlatShading});
      const tailplane = new THREE.Mesh(geomTailplane, matTailplane);
      tailplane.position.set(-35, 25, 0);
      tailplane.castShadow =true;
      tailplane.receiveShadow = true;
      this.mesh.add(tailplane);

      // create wing
      const geomSideWing = new THREE.BoxGeometry(40,8,150,1,1,1);
      const matSideWing = new THREE.MeshPhongMaterial({color:0xff604e, flatShading:THREE.FlatShading});
      const sideWing = new THREE.Mesh(geomSideWing, matSideWing);
      sideWing.position.set(0,0,0);
      sideWing.castShadow = true;
      sideWing.receiveShadow = true;
      this.mesh.add(sideWing);

      // Propeller
      const geomPropeller = new THREE.BoxGeometry(20,10,10,1,1,1);
      const matPropeller = new THREE.MeshPhongMaterial({color:0xa52a2a , flatShading:THREE.FlatShading});
      this.propeller = new THREE.Mesh(geomPropeller, matPropeller);
      this.propeller.castShadow = true;
      this.propeller.receiveShadow = true;

      // Blades
      const geomBlade = new THREE.BoxGeometry(1,100,20,1,1,1);
      const matBlade = new THREE.MeshPhongMaterial({color:0x654321, flatShading:THREE.FlatShading});

      const blade = new THREE.Mesh(geomBlade, matBlade);
      blade.position.set(8,0,0);
      blade.castShadow = true;
      blade.receiveShadow = true;
      this.propeller.add(blade);
      this.propeller.position.set(50,0,0);
      this.mesh.add(this.propeller);
    },
    Cloud: function(){
      this.mesh = new THREE.Object3D();
      this.mesh.name = "cloud";
      const geom = new THREE.CubeGeometry(20,20,20);
      const mat = new THREE.MeshPhongMaterial({
        color:0xffffff ,
      });

      const nBlocs = 3+Math.floor(Math.random()*3);
      for (let i=0; i<nBlocs; i++ ){
        const m = new THREE.Mesh(geom.clone(), mat);
        m.position.x = i*15;
        m.position.y = Math.random()*10;
        m.position.z = Math.random()*10;
        m.rotation.z = Math.random()*Math.PI*2;
        m.rotation.y = Math.random()*Math.PI*2;
        const s = .1 + Math.random()*.9;
        m.scale.set(s,s,s);
        m.castShadow = true;
        m.receiveShadow = true;
        this.mesh.add(m);
      }
    },
    Sky: function(globalContext){
      this.mesh = new THREE.Object3D();
      this.nClouds = 20;
      const stepAngle = Math.PI*2 / this.nClouds;
      for(let i=0; i<this.nClouds; i++){
        const c = new globalContext.Cloud();
        const a = stepAngle*i;
        const h = 750 + Math.random()*200;
        c.mesh.position.y = Math.sin(a)*h;
        c.mesh.position.x = Math.cos(a)*h;
        c.mesh.position.z = -400-Math.random()*400;
        c.mesh.rotation.z = a + Math.PI/2;
        const s = 1+Math.random()*2;
        c.mesh.scale.set(s,s,s);
        this.mesh.add(c.mesh);
      }
    },
    Sea: function(){
      const geom = new THREE.CylinderGeometry(600,600,800,40,10);
      geom.applyMatrix(new THREE.Matrix4().makeRotationX(-Math.PI/2));
      const mat = new THREE.MeshPhongMaterial({
        color:0x03d0d0 ,
        transparent:true,
        opacity:.6,
        shading:THREE.FlatShading,
      });
      this.mesh = new THREE.Mesh(geom, mat);
      this.mesh.receiveShadow = true;
    },
    createPlane(){
      this.airplane = new this.airPlane();
      this.airplane.mesh.scale.set(.25,.25,.25);
      this.airplane.mesh.position.y = 100;
      this.scene.add(this.airplane.mesh);
    },
    createSea(){
      this.sea = new this.Sea();
      this.sea.mesh.position.y = -600;
      this.scene.add(this.sea.mesh);
    },
    createSky(){
      this.sky = new this.Sky(this);
      this.sky.mesh.position.y = -500;
      this.scene.add(this.sky.mesh);
    },
    loop(){
      this.updatePlane();
      this.sea.mesh.rotation.z += .005;
      this.sky.mesh.rotation.z += .01;
      this.renderer.render(this.scene, this.camera);
      requestAnimationFrame(this.loop);
    },
    updatePlane(){
      const targetY = this.normalize(this.mousePos.y,-.75,.75,25, 175);
      const targetX = this.normalize(this.mousePos.x,-.75,.75,-100, 100);
      this.airplane.mesh.position.y = targetY;
      this.airplane.mesh.position.x = targetX;
      this.airplane.propeller.rotation.x += 0.3;
    },
    normalize(v,vmin,vmax,tmin, tmax){
      const nv = Math.max(Math.min(v,vmax), vmin);
      const dv = vmax-vmin;
      const pc = (nv-vmin)/dv;
      const dt = tmax-tmin;
      const tv = tmin + (pc*dt);
      return tv;
    },
    handleMouseMove(event) {
      const tx = -1 + (event.clientX / this.WIDTH)*2;
      const ty = 1 - (event.clientY / this.HEIGHT)*2;
      this.mousePos = {x:tx, y:ty};
    },
    init(){
      document.addEventListener('mousemove', this.handleMouseMove, false);
      this.createScene();
      this.createLights();
      this.createPlane();
      this.createSea();
      this.createSky();
      this.loop();
    }
  }
}
</script>

<!-- Add "scoped" attribute to limit CSS to this component only -->
<style scoped>
.hello{
  background-color: darksalmon;
}
</style>
