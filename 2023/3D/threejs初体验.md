当网站用上了3D，会迅速脱颖而出。无论你想创建产品展示、官网页面、数据可视化、音视频、广告、游戏、CAD、CAM或者BIM软件，任何应用程序，使用three.js创建的3D网页都比其他网页更胜一筹。
# 引入three
```html
<script async src="https://unpkg.com/es-module-shims@1.3.6/dist/es-module-shims.js"></script>
<script type="importmap">
  {
  "imports": {
    "three": "https://unpkg.com/three@0.149.0/build/three.module.js",
    "three/addons/": "https://unpkg.com/three@0.149.0/examples/jsm/"
  }
  }
</script>
<script type="module">
import * as THREE from 'three'
import { OrbitControls } from 'three/addons/controls/OrbitControls.js'
</script>
```
# 编写玩具车
```html
<!DOCTYPE html>
<html>
<head>
  <meta charset="UTF-8">
  <meta name="viewport" content="width=device-width, initial-scale=1.0">
  <title>A minimalistic car</title>
  <style>
    body {margin:0}
  </style>
</head>
<body>
  <script async src="https://unpkg.com/es-module-shims@1.3.6/dist/es-module-shims.js"></script>

  <script type="importmap">
    {
    "imports": {
      "three": "https://unpkg.com/three@0.149.0/build/three.module.js",
      "three/addons/": "https://unpkg.com/three@0.149.0/examples/jsm/"
    }
    }
  </script>
<script type="module">
import * as THREE from 'three'
import { OrbitControls } from 'three/addons/controls/OrbitControls.js'

const scene = new THREE.Scene()

const ambientLight = new THREE.AmbientLight(0xffffff, 0.6)
scene.add(ambientLight)
const directionalLight = new THREE.DirectionalLight(0xffffff, 0.8)
directionalLight.position.set(200,500,300) // (X,Y,Z)->(0,0,0), other rays parallel to it. 
scene.add(directionalLight)

const aspectRatio = window.innerWidth / window.innerHeight
const cameraWidth = 150
const cameraHeight = cameraWidth / aspectRatio
const camera = new THREE.OrthographicCamera(cameraWidth/-2,cameraWidth/2,cameraHeight/2,cameraHeight/-2,0,1000) // left,right,top,bottom,near plane,far plane
camera.position.set(200,200,200)
camera.lookAt(0,10,0)  // car's center

const renderer = new THREE.WebGLRenderer({antialias:true})
renderer.setSize(window.innerWidth, window.innerHeight)
renderer.render(scene, camera)

document.body.appendChild(renderer.domElement)
const controls = new OrbitControls( camera, renderer.domElement );

function createWheels() {
  const geometry = new THREE.BoxBufferGeometry(12,12,33)
  const material = new THREE.MeshLambertMaterial({color: 0x333333})
  const wheel = new THREE.Mesh(geometry, material)
  return wheel
}
function createCar() {
  const car = new THREE.Group()
  const backWheel = createWheels()
  backWheel.position.y = 6
  backWheel.position.x = -18
  car.add(backWheel)
  const frontWheel = createWheels()
  frontWheel.position.y = 6
  frontWheel.position.x = 18
  car.add(frontWheel)
  const main = new THREE.Mesh(new THREE.BoxBufferGeometry(60,15,30), new THREE.MeshLambertMaterial({color:0x78b14b}))
  main.position.y = 12
  car.add(main)
  
  const carFrontTexture = getCarFrontTexture()
  const carBackTexture = getCarFrontTexture()
  const carRightSideTexture = getCarSideTexture()
  const carLeftSideTexture = getCarSideTexture()
  carLeftSideTexture.center = new THREE.Vector2(0.5,0.5)
  carLeftSideTexture.rotation = Math.PI 
  carLeftSideTexture.flipY = false 

  const cabin = new THREE.Mesh(new THREE.BoxBufferGeometry(33,12,24), [
    new THREE.MeshLambertMaterial({map: carFrontTexture}),
    new THREE.MeshLambertMaterial({map: carBackTexture}),
    new THREE.MeshLambertMaterial({color: 0xffffff}), // top
    new THREE.MeshLambertMaterial({color: 0xffffff}), // bottom
    new THREE.MeshLambertMaterial({map: carRightSideTexture}),
    new THREE.MeshLambertMaterial({map: carLeftSideTexture}),
])
  cabin.position.x = -6
  cabin.position.y = 25.5
  car.add(cabin)
  return car
}

const car = createCar()
scene.add(car)

renderer.render(scene, camera)

function getCarFrontTexture() {
  const canvas = document.createElement("canvas")
  canvas.width = 64
  canvas.height = 32
  const context = canvas.getContext("2d")
  context.fillStyle = "#ffffff"
  context.fillRect(0,0,64,32)
  context.fillStyle = "#666666"
  context.fillRect(8,8,48,24)
  return new THREE.CanvasTexture(canvas)
}
function getCarSideTexture() {
  const canvas = document.createElement("canvas")
  canvas.width = 128
  canvas.height = 32
  const context = canvas.getContext("2d")
  context.fillStyle = "#ffffff"
  context.fillRect(0,0,128,32)
  context.fillStyle = "#666666"
  context.fillRect(10,8,38,24)
  context.fillRect(58,8,60,24)
  return new THREE.CanvasTexture(canvas)
}

function animate() {
  requestAnimationFrame(animate)

  renderer.render(scene, camera)
}
animate()
</script>
</body>
</html>
```
该代码可以保存成html然后直接用浏览器查看。这是仅用cube组成的小汽车，后面我们可以再增加sphere,cylinder等组成小火车并让它滚动。
# Load glTF 3D模型
显然，用上面的方法来写模型太复杂太麻烦了。为了创建模型，必须使用一个建模软件，然后用threejs渲染，做各自擅长的事。
```html
<!DOCTYPE html>
<html>
<head>
  <meta charset="UTF-8">
  <meta name="viewport" content="width=device-width, initial-scale=1.0">
  <title>A minimalistic car</title>
  <style>
    body {margin:0}
  </style>
</head>
<body>
  <script async src="https://unpkg.com/es-module-shims@1.3.6/dist/es-module-shims.js"></script>

  <script type="importmap">
    {
    "imports": {
      "three": "https://unpkg.com/three@0.149.0/build/three.module.js",
      "three/addons/": "https://unpkg.com/three@0.149.0/examples/jsm/"
    }
    }
  </script>
<script type="module">
import * as THREE from 'three'
import { OrbitControls } from 'three/addons/controls/OrbitControls.js'
import { GLTFLoader } from 'three/addons/loaders/GLTFLoader.js'

const scene = new THREE.Scene()
const renderer = new THREE.WebGLRenderer({antialias:true})
renderer.setSize(window.innerWidth, window.innerHeight)

const ambientLight = new THREE.AmbientLight(0xffffff, 0.6)
scene.add(ambientLight)
const directionalLight = new THREE.DirectionalLight(0xffffff, 0.8)
directionalLight.position.set(200,500,300) // (X,Y,Z)->(0,0,0), other rays parallel to it. 
scene.add(directionalLight)

const aspectRatio = window.innerWidth / window.innerHeight
const cameraWidth = 150
const cameraHeight = cameraWidth / aspectRatio
const camera = new THREE.OrthographicCamera(cameraWidth/-2,cameraWidth/2,cameraHeight/2,cameraHeight/-2,0,1000) // left,right,top,bottom,near plane,far plane
camera.position.set(200,200,200)
camera.lookAt(0,10,0)

const controls = new OrbitControls( camera, renderer.domElement );

const loader = new GLTFLoader()
loader.load('three.js/examples/models/gltf/Parrot.glb', function(gltf) {
  scene.add(gltf.scene)

  renderer.render(scene, camera)
  document.body.appendChild(renderer.domElement)
})

function animate() {
  requestAnimationFrame(animate)

  renderer.render(scene, camera)
}
animate()
</script>
</body>
</html>
```
这里的three.js目录可以直接git clone https://github.com/mrdoob/three.js.git 得到。
# Load fbx
```html
<!DOCTYPE html>
<html>
<head>
  <meta charset="UTF-8">
  <meta name="viewport" content="width=device-width, initial-scale=1.0">
  <title>A minimalistic car</title>
  <style>
    body {margin:0}
  </style>
</head>
<body>
  <script async src="https://unpkg.com/es-module-shims@1.3.6/dist/es-module-shims.js"></script>
  <script type="importmap">
    {"imports": {"three": "https://unpkg.com/three@0.149.0/build/three.module.js", "three/addons/": "https://unpkg.com/three@0.149.0/examples/jsm/"}}
  </script>
<script type="module">
import * as THREE from 'three'
import { OrbitControls } from 'three/addons/controls/OrbitControls.js'
import { FBXLoader } from 'three/addons/loaders/FBXLoader.js'

const scene = new THREE.Scene()
const renderer = new THREE.WebGLRenderer({antialias:true,alpha:true})
renderer.setSize(window.innerWidth, window.innerHeight)

const ambientLight = new THREE.AmbientLight(0xffffff, 0.6)
scene.add(ambientLight)
const directionalLight = new THREE.DirectionalLight(0xffffff, 0.8)
directionalLight.position.set(200,500,300) // (X,Y,Z)->(0,0,0), other rays parallel to it. 
scene.add(directionalLight)


const camera = new THREE.PerspectiveCamera(75,window.innerWidth/window.innerHeight,0.1,1000)
camera.position.set(0.8,1.4,1.0)
const controls = new OrbitControls( camera, renderer.domElement )
controls.enableDamping = true 

const loader = new FBXLoader()

loader.load('three.js/examples/models/fbx/Samba Dancing.fbx', function(object) { 
  console.log(object)
  object.scale.set(0.005,0.005,0.005)
  scene.add(object)

  renderer.render(scene, camera)
  document.body.appendChild(renderer.domElement)
})

function animate() {
  requestAnimationFrame(animate)
  controls.update()
  renderer.render(scene, camera)
}
animate()
</script>
</body>
</html>
```
# Load ifc
```javascript
const loader = new IFCLoader()
loader.load('three.js/examples/models/ifc/rac_advanced_sample_project.ifc', function(object) {
  console.log(object)
  object.scale.set(0.1,0.1,0.1)
  scene.add(object)

  renderer.render(scene, camera)
})
```
Load不同格式的模型只是更换一下使用的Loader类，其余代码同上。  
如果遇到找不到web-ifc.wasm,则把three.js/examples/jsm/loaders/ifc/web-ifc.wasm拷贝至项目根目录。
# Load Revit
之前用va3c插件，把revit导出成json,再通过rvt-viewer查看，目前来看并非最优解。  
可以尝试把revit导出成gltf。
# 关于USD
## 简易版
1. 如果用户提供usdz模型文件，则直接上传；如果用户提供usd文件，则服务器端通过usdzip打包成usdz再上传；如果用户提供非usd/usdz模型文件，则服务器端通过omniverse重新导出成usd文件，再usdzip，再上传  
2. 网页端集成 [three-usdz-loader](https://www.npmjs.com/package/three-usdz-loader)  
3. 网页端加载 usdz文件路径，显示  

举例: 用 [示例文件](https://docs.fileformat.com/3d/sample.usdz) 上传到 [usdz-viewer](https://www.usdz-viewer.net/)  
这种方式目前遇到的问题是，usd打包成usdz后，web加载会缺少光照。
## 完善版
1. usd文件不需要打包成usdz,而是在服务器端解析或转换文件格式  
2. 网页端通过threejs渲染各个Mesh  
3. 网页端可以适当改进各个Mesh的显示效果  
4. 文件过大时，网页端得考虑LOD
## 后续
当查看器初步完成后，再考虑增加深度学习算法，以及BIM的管理等纵深流程。
