<template>
  <div id="app">
    <div class="controller">
      <button @click="play">paly</button>
      <button @click="pause">pause</button>
      <button @click="rotation">rotation</button>
      <button @click="changeMetarial('default')">defaultMaterial</button>
      <button @click="changeMetarial('MeshMatcapMaterial')">MeshMatcapMaterial</button>
      <button @click="BodychangeMetarial">BodyMeshMatcapMaterial</button>


    </div>
    <div class="block">
      <span class="demonstration">rotatinX</span>
      <el-slider :max="700" v-model="rotatinX" :format-tooltip="formatTooltip"></el-slider>
    </div>
    <div class="block">
      <span class="demonstration">rotatinY</span>
      <el-slider :max="700" v-model="rotatinY" :format-tooltip="formatTooltip"></el-slider>
    </div>
    <div class="block">
      <span class="demonstration">rotatinZ</span>
      <el-slider :max="700" v-model="rotatinZ" :format-tooltip="formatTooltip"></el-slider>
    </div>
    <el-radio-group v-model="radio">
      <el-radio :label="3">备选项</el-radio>
      <el-radio :label="6">备选项</el-radio>
      <el-radio :label="9">备选项</el-radio>
    </el-radio-group>
    <div class="block">
      <span class="demonstration">颜色 {{color2}}</span>
      <el-color-picker @change="colorchange" v-model="color2"></el-color-picker>
    </div>
    <div>
      <h1>相机位置</h1>
     <div>x:{{capos.x}}</div>
     <div>y:{{capos.y}}</div>
     <div>z:{{capos.z}}</div>

    </div>
    <div class="canvas-box">
      <canvas id="canvas"></canvas>
    </div>
    <div class="canvas-box">
      <canvas id="canvas2"></canvas>
    </div>
  </div>
</template>

<script>
import * as THREE from 'three'
import { GLTFLoader } from 'three/examples/jsm/loaders/GLTFLoader.js';
import { OrbitControls } from 'three/examples/jsm/controls/OrbitControls.js';
export default {
  name: 'App',
  data() {
    return {
      scene:{},
      camera:{},
      renderer:{},
      controls: null,
      angle:0,
      timer:null,
      group:{},
      rotatinX:0,
      rotatinY:0,
      rotatinZ:0,
      color2:'',
      defaultMaterial:null,
      radio: 3
    }
  },
  watch: {
    rotatinX : function (val) {
      this.group.rotation.x = val /100
    },
    rotatinY : function (val) {
      this.group.rotation.y = val /100
    },
    rotatinZ : function (val) {
      this.group.rotation.z = val /100
    }
  },
  mounted() {
    this.init ()
  },
  computed: {
    capos () {
      if(this.camera.position) {
        return this.camera.position
      }
      return {
        x:0,
        y:0,
        z:0
      }
    }
  },
  methods: {
    BodychangeMetarial () {
      let bodyMaterial = new THREE.MeshPhysicalMaterial( {
        color: 0xff0000, metalness: 1.0, roughness: 0.5, clearcoat: 1.0, clearcoatRoughness: 0.03, sheen: 0.5
      } )
      this.group.traverseVisible(item => {
        if(item.isMesh && item.name.includes('body')) {
          item.material = bodyMaterial.clone()
        }
      })
      console.log(bodyMaterial);
    },
    init () {
      const params = {
				exposure: 1.0,
				toneMapping: 'ACESFilmic'
			};
      const toneMappingOptions = {
				None: THREE.NoToneMapping,
				Linear: THREE.LinearToneMapping,
				Reinhard: THREE.ReinhardToneMapping,
				Cineon: THREE.CineonToneMapping,
				ACESFilmic: THREE.ACESFilmicToneMapping,
				Custom: THREE.CustomToneMapping
			};
      const canvas = document.querySelector('#canvas');
      this.renderer = new THREE.WebGLRenderer({canvas,antialias:true, alpha:true});
      this.renderer.toneMapping = toneMappingOptions[ params.toneMapping ];
      this.renderer.toneMappingExposure = params.exposure;
      const fov = 30;
      // const aspect = window.innerWidth / window.innerHeight;  // 相机默认值
      const aspect = 2;  // 相机默认值
      const near = 1;
      const far = 1300;
      this.camera = new THREE.PerspectiveCamera(fov, aspect, near, far);

      this.camera.position.z = 200;
      this.camera.position.x = 0
      this.camera.position.y = 100
      const helper = new THREE.CameraHelper( this.camera );
      this.scene = new THREE.Scene();

      this.scene.add(helper)
      const color = 0xFFFFFF;
      const intensity = 1;
      const light = new THREE.DirectionalLight(color, intensity);
      const light2 = new THREE.DirectionalLight(color, intensity);
      const light3 = new THREE.DirectionalLight(color, intensity);
      const light4 = new THREE.DirectionalLight(color, intensity);
      const light5 = new THREE.DirectionalLight(color, intensity);
      const light6 = new THREE.DirectionalLight(color, intensity);
      const light7 = new THREE.DirectionalLight(color, intensity);
      const light8 = new THREE.DirectionalLight(color, intensity);
      light.position.set(50,0,0);
      light2.position.set(0,50,0)
      light3.position.set(0,0,50)
      light4.position.set(-50,0,0)
      light5.position.set(0,-50,0)
      light6.position.set(0,0,-50)
      light7.position.set(0,0,-50)
      light8.position.set(0,0,-50)
      this.scene.add(light);
      this.scene.add(light2);
      this.scene.add(light3);
      this.scene.add(light4);
      this.scene.add(light5);
      this.scene.add(light6);
      this.scene.add(light7);
      this.scene.add(light8);

      const boxWidth = 1;
      const boxHeight = 1;
      const boxDepth = 1;
      const geometry = new THREE.BoxGeometry(boxWidth, boxHeight, boxDepth);

      const material = new THREE.MeshBasicMaterial({color: 0x44aa88});

      const cube = new THREE.Mesh(geometry, material);

      this.scene.add(cube)

      const gltfLoader = new GLTFLoader();
      const url = 'models/test1.glb';
      gltfLoader.load(url, (gltf) => {
        console.log(gltf);
        const root = gltf.scene;
        root.children[0].position.x = 0
        root.children[0].position.z = 0
        root.children[0].position.y = 0
        const axesHelper = new THREE.AxesHelper( 100 );
        root.add( axesHelper );
        root.rotation.x = 0
        root.rotation.y = 0
        root.rotation.z = 0

        this.group = root
        this.defaultMaterial = root.children[0].children[0].material.clone()
        console.log(this.group);
        console.log(root.children[0].position);
        this.scene.add(root);
      });

      // this.renderer.render(this.scene, this.camera);
      const controls = new OrbitControls( this.camera, this.renderer.domElement );
     
      controls.update();
      requestAnimationFrame(this.render);
    },
    render () {
      this.renderer.render(this.scene, this.camera);
      requestAnimationFrame(this.render);
    },
    pause () {
      clearInterval(this.timer)
      this.timer = null
      this.renderer.render(this.scene, this.camera);
    },
    play () {
      this.cameraMove()
    },
    cameraMove () {
        if(this.timer) return false
        this.timer = setInterval(() => {
           this.angle+=0.005
          // 重新设置相机位置，相机在XOY平面绕着坐标原点旋转运动
          this.camera.position.x=200*Math.sin(this.angle)
          this.camera.position.z=200*Math.cos(this.angle)
          // this.camera.position.y=200*Math.cos(this.angle)
          this.camera.lookAt(0,0,0)
          this.renderer.render(this.scene, this.camera);
        },16)
       
        // requestAnimationFrame(this.cameraMove)
    },
    rotation () {
       if(this.timer) return false
       this.angle = this.rotatinY
        this.timer = setInterval(() => {
          this.angle += 0.1
          // 重新设置相机位置，相机在XOY平面绕着坐标原点旋转运动
          this.group.rotation.y = this.angle
          this.rotatinY = this.group.rotation.y
          // this.camera.position.y=200*Math.cos(this.angle)
          // this.camera.lookAt(0,0,0)
          this.renderer.render(this.scene, this.camera);
        },16)
    },
    formatTooltip(val) {
      return val / 100;
    },
    colorchange (color) {
      console.log(color);
      // let root = this.group.children[0]
      this.group.traverseVisible( mesh => {
        if(mesh.isMesh && mesh.name.includes('body')) {
          // mesh.material.color.set(color)
          mesh.material.color.set(color)
        }
      })
      
    },
    changeMetarial (material) {
      // let MatcapMaterial = new THREE.MeshMatcapMaterial({color: this.color2})
      let MatcapMaterial = new THREE.MeshMatcapMaterial()
      let root = this.group.children[0]
      switch (material) {
        case 'default':
           root.children.forEach (mesh => {
              if(mesh.isMesh) {
                mesh.material = this.defaultMaterial.clone()
              }
            })
            return
        case 'MeshMatcapMaterial':
          //  root.children.forEach (mesh => {
          //     if(mesh.isMesh) {
          //       mesh.material=MatcapMaterial
          //       console.log(mesh.name, '123');
          //     }
          //   })
          this.group.traverseVisible( mesh => {
            if(mesh.isMesh) {
              mesh.material = MatcapMaterial.clone()
                console.log(mesh.material.uuid, '123',mesh.name);

            }
          })
          return;
        default:
          break;
      }
    }
  },
}
</script>

<style>
#app {
  width: 50%;
  margin: 0 auto;
}
* {
  padding: 0;
  margin: 0;
}
#canvas{
 width: 500px;
height: 250px;
background: #000000;
/* position: absolute;
top: 50%;
left: 50%;
transform: translate(-50%,-50%); */
}
.block {
  width: 50%;
  margin-left: 20px;
}
</style>
