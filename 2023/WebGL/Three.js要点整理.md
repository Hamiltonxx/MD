Threejs让基于WebGL的3D操作变得简单，有以下方面:  
1. 创建geometry且在浏览器渲染  
2. 在Scene中移动物体  
3. 给物体加texture和material  
4. 使用不同的光源给Scene照明  
5. 加载建模软件生成的3D模型  
6. 给Scene增加后处理效果  
7. 创建和自定义shader  
8. 创建、查看点云  
9. 创建VR和AR Scene  

# 创建第一个3D Scene
```
<!DOCTYPE html>
<html>
<head>
  <meta charset="UTF-8">
  <meta name="viewport" content="width=device-width, initial-scale=1.0">
  <title>Setting Up</title>
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
import Stats from 'three/addons/libs/stats.module'
import { OrbitControls } from 'three/addons/controls/OrbitControls'
// create a scene
const scene = new THREE.Scene()
scene.backgroundColor = 0xffffff
scene.fog = new THREE.Fog(0xffffff, 0.0025, 50)  // objects futher away will hide
// setup camera
const camera = new THREE.PerspectiveCamera(75, window.innerWidth / window.innerHeight, 0.1, 1000)
camera.position.x = -3
camera.position.z = 8
camera.position.y = 2
// setup the renderer and attach to canvas
const renderer = new THREE.WebGLRenderer({antialias:true})
renderer.outputEncoding = THREE.sRGBEncoding 
renderer.shadowMap.enabled = true 
renderer.shadowMap.type = THREE.VSMShadowMap
renderer.setSize(window.innerWidth, window.innerHeight)
renderer.setClearColor(0xffffff)
document.body.appendChild(renderer.domElement)
// add lights
scene.add(new THREE.AmbientLight(0x666666))
const dirLight = new THREE.DirectionalLight(0xaaaaaa)
dirLight.position.set(5,12,8)
dirLight.castShadow = true 
scene.add(dirLight)
// add meshes
const cubeGeometry = new THREE.BoxGeometry()
const cubeMaterial = new THREE.MeshPhongMaterial({color:0x0000FF})
const cube = new THREE.Mesh(cubeGeometry, cubeMaterial)
cube.position.x = -1
cube.castShadow = true 
scene.add(cube)
const torusKnotGeometry = new THREE.TorusKnotGeometry(0.5,0.2,100,100)
const torusKnotMat = new THREE.MeshStandardMaterial({color: 0x00ff88, roughness: 0.1})
const torusKnot = new THREE.Mesh(torusKnotGeometry, torusKnotMat)
torusKnot.castShadow = true
torusKnot.position.x = 2
scene.add(torusKnot)

const groundGeometry = new THREE.PlaneGeometry(10000,10000)
const groundMaterial = new THREE.MeshLambertMaterial({color:0xffffff})
const ground = new THREE.Mesh(groundGeometry, groundMaterial)
ground.position.set(0,-2,0)
ground.rotation.set(Math.PI/-2,0,0)
ground.receiveShadow = true 
scene.add(ground)
// add orbitcontrols to pan around the scene using the mouse
const orbitControls = new OrbitControls(camera, renderer.domElement)
// add statistics to monitor the framerate
// render the scene
function animate() {
  requestAnimationFrame(animate)
  orbitControls.update()
  renderer.render(scene, camera)
}
animate()
</script>
</body>
</html>
```
## mesh的函数和属性
为了创建一个mesh,我们需要一个geometry和一或多个material. 把mesh加到scene里，它就会被渲染。  
mesh的属性有 position, rotation, scale, translate, lookAt, visible, castShadow  
rotate是沿着(父场景的)轴旋转。也有rotateOnWorldAxis沿着主THREE.Scene旋转。rotateOnAxis沿着自己的轴。  
```javascript
// add gui
const gui = new GUI()
const props = {
  positionX: 0, 
  positionY: 1,
  positionZ: 0,
  scaleX: 1,
  scaleY: 1,
  scaleZ: 1,
  rotationX: 0,
  rotationY: 0,
  rotationZ: 0
} 
gui.add(props, 'positionX', -20,20,1)
gui.add(props, 'positionY', -20,20,1)
gui.add(props, 'positionZ', -20,20,1)
gui.add(props, 'scaleX', -20,20,0.1)
gui.add(props, 'scaleY', -20,20,0.1)
gui.add(props, 'scaleZ', -20,20,0.1)
gui.add(props, 'rotationX',-180,180,1)
gui.add(props, 'rotationY',-180,180,1)
gui.add(props, 'rotationZ',-180,180,1)

// render the scene
function animate() {
  requestAnimationFrame(animate)
  orbitControls.update()
  mesh.position.set(props.positionX,props.positionY,props.positionZ)
  mesh.scale.set(props.scaleX,props.scaleY,props.scaleZ)
  mesh.rotation.set(props.rotationX*Math.PI/180,props.rotationY*Math.PI/180,props.rotationZ*Math.PI/180)
  renderer.render(scene, camera)
}
animate()
```
<img src="https://pro-developer.oss-cn-shanghai.aliyuncs.com/74/img/1677633901600_Screenshot 2023-03-01 at 09.23.32.png" width="60%">  

## 换不同camera
threejs有正交相机(orthographic camera)和透视相机(perspective camera)之分。  
perspective camera模拟人眼看到的景象(渐远渐小),是场景渲染中用得最普遍的投影模式。orthographic camera则无论物体距离相机远近，在渲染时保持大小不变。  
还有一些特殊的camera用于3D眼镜和VR设备观看。Three.js对WebVR标准有一些实验性的支持。这时camera使用THREE.StereoCamera,设置renderer.vr.enabled = true。  
## lookAt
我们需要把camera放置在scene中的某个位置，才能开始渲染。一般，camera指向scene中心点(0,0,0).我们也可以改变它的指向
```javascript
camera.lookAt(new THREE.Vector3(x,y,z))
``` 

# Light
WebGL本身没有内置的lighting. 不用Three.js，你就得自己写专门的shader程序来模仿各种光照，这很难。如果感兴趣可以参考 [Lighting in WebGL](https://developer.mozilla.org/en-US/docs/Web/API/WebGL_API/Tutorial/Lighting_in_WebGL)  
Light种类有:  
1. AmbientLight  
2. PointLight  
3. SpotLight  
4. DirectionalLight  
5. HemisphereLight  
6. RectAreaLight  
7. LightProbe  
8. LensFlare  

常用的Light是AmbientLight,PointLight,SpotLight,DirectionalLight. 而HemisphereLight,RectAreaLight,LightProbe,LensFlare则用于特殊目的。  
## AmbientLight
AmbientLight让颜色布于全局，没有特别的光照来源，也就没有shadow。
## DirectionalLight、PointLight、SpotLight
DirectionalLight: 平行光  
PointLight: 四散光  
SpotLight: 点光
## Color
创建Color对象有多种方法
```javascript
new THREE.Color("#ababab")
new THREE.Color(0xababab)
new THREE.Color("rgb(255,0,0)")
new THREE.Color('skyblue')
new THREE.Color(1,0,0)
```

# Material
Material就是物体的皮肤，决定了几何的外表看起来如何。怎样利用Matrial来让3D物体看起来更好看？Three.js给出的Material有:  
1. MeshBasicMaterial  
2. MeshDepthMaterial  
3. MeshNormalMaterial  
4. MeshLambertMaterial  
5. MeshPhongMaterial  
6. MeshStandardMaterial  
7. MeshPhysicalMaterial  
8. MeshToonMaterial  
9. ShadowMaterial  
10. ShaderMaterial  
11. LineBasicMaterial  
12. LineDashMaterial  

上面这些类都继承自THREE.Material,所以他们有一些共同的属性。可以分为基础属性、混合属性、高级属性。大多数Material允许使用图片作为texture(比如木头纹理或石头纹理)。
## 基础属性
1. id  
2. uuid  
3. name  
4. opacity  
5. transparent  
6. visible  
7. side  
8. needsUpdate  
9. colorWrite  
10. flatShading  
11. lights  
12. premultipliedAlpha  
13. dithering  
14. shadowSide  
15. vertexColors  
16. fog
## 混合属性
1. blending  
2. blendSrc  
3. blendSrcAlpha  
4. blendDst  
5. blendDstAlpha  
6. blendEquation
## 高级属性
1. depthTest  
2. depthWrite  
3. depthFunc  
4. polygonOffset, polygonOffsetFactor, polygonOffsetUnits
5. Alphatest
## MeshBasicMaterial, MeshDepthMaterial, MeshNormalMaterial
```javascript
const material = new THREE.MeshBasicMaterial({
  color: 0xff0000,
  name: 'material-1',
  opacity: 0.5,
  transparency: true
})
```
MeshBasicMaterial不包含Light,呈现简单的平面多边形，有显示geometry wireframe的选项。  
MeshDepthMaterial,物体外观不是由light或material属性决定，而是由距离相机的远近决定。可以用这个结合其它material来打造fading效果。  
```javascript
import {Group,Mesh,MeshDepthMaterial,MeshBasicMaterial,BoxGeometry} from 'three'
function createMultiMaterialObject(geometry, materials) {
  const group = new Group()
  for(let i=0, l=materials.length; i<l; i++) {
    group.add(new Mesh(geometry,materials[i]))
  }
  return group;
}

const material1 = new MeshDepthMaterial()
const material2 = new MeshBasicMaterial({color:0xffff00})
const geometry = new BoxGeometry(0.5,0.5,0.5)
const cube = createMultiMaterialObject(geometry,[material2,material1])
```
MeshNormalMaterial,每面的颜色由法线(normal)决定。
## 一个mesh多种material
```javascript
import {MeshBasicMaterial,BoxGeometry,Mesh,Group} from 'three'
import { OrbitControls } from 'three/addons/controls/OrbitControls'
import {init_scene, init_camera, init_renderer, init_lights} from "./utils.js"
const scene = init_scene()
const camera = init_camera()
const renderer = init_renderer()
init_lights(scene)
// add meshes
const mat1 = new MeshBasicMaterial({color:0x777777})
const mat2 = new MeshBasicMaterial({color:0xff0000})
const mat3 = new MeshBasicMaterial({color:0x00ff00})
const mat4 = new MeshBasicMaterial({color:0x0000ff})
const mat5 = new MeshBasicMaterial({color:0x66aaff})
const mat6 = new MeshBasicMaterial({color:0xffaa66})
const mats = [mat1,mat2,mat3,mat4,mat5,mat6]
const cubeGeometry = new BoxGeometry(1,1,1,10,10,10)
const cubeMesh = new Mesh(cubeGeometry, mats)
console.log(cubeGeometry)
scene.add(cubeMesh)
// add orbitcontrols to pan around the scene using the mouse
const orbitControls = new OrbitControls(camera, renderer.domElement)
// render the scene
function animate() {
  requestAnimationFrame(animate)
  orbitControls.update()
  renderer.render(scene, camera)
}
animate()
```
<img src="https://pro-developer.oss-cn-shanghai.aliyuncs.com/74/img/1677634124764_Screenshot 2023-03-01 at 09.28.23.png" width="60%">  

## Advanced materials
MeshLambertMaterial(漫反射), MeshPhongMaterial(镜面反射), MeshToonMaterial(卡通), ShadowMaterial, MeshStandardMaterial, MeshPhysicalMaterial, ShaderMaterial  
LambertMaterial也支持wireframe.

# Geometry
CircleGeometry, RingGeometry, PlaneGeometry, ShapeGeometry, BoxGeometry, SphereGeometry, CylinderGeometry, ConeGeometry, TorusGeometry, TorusKnotGeometry, PolyhedronGeometry, IcosahedronGeometry, OctahedronGeometry, TetraHedronGeometry, DodecahedronGeometry
## 2D geometry
CircleGeometry, RingGeometry, PlaneGeometry, ShapeGeometry
## 3D geometry
BoxGeometry, width,height,depth, widthSegments, 把面沿x轴分segments。segments越多，面越多。  
SphereGeometry

# Points and Sprites
THREE.Points(有时也叫Sprites)，用来创建很小的矩形，来模拟雨、雪、烟及其它效果。这样可以把点的集合组成geometry,就能单独控制这些点。
```javascript
const createPoints = () => {
  const points = []
  for (let x=-15; x<15; x++) {
    for (let y=-10; y<10; y++) {
      let point = new Vector3(x/4, y/4, 0)
      points.push(point)
    }
  }
  const colors = new Float32Array(points.length*3)
  points.forEach((e,i) => {
    const c = new Color(Math.random()*0xffffff)
    colors[i*3] = c.r 
    colors[i*3+1] = c.g 
    colors[i*3+2] = c.b
  })
  const geom = new BufferGeometry().setFromPoints(points)
  geom.setAttribute('color', new BufferAttribute(colors,3,true))
  return geom
}
const material = new PointsMaterial({size:0.1, vertexColors:true, color:0xffffff})
const points = new Points(createPoints(), material)
scene.add(points)
```
```javascript
const createPoints = () => {
  const points = []
  const range = 15
  for (let i=0; i<15000; i++) {
    let particle = new Vector3(Math.random()*range-range/2,Math.random()*range-range/2,Math.random()*range-range/2)
    points.push(particle)
  }
  const colors = new Float32Array(points.length * 3)
  points.forEach((e,i) => {
    const c = new Color(Math.random()*0xffffff)
    colors[i*3] = c.r 
    colors[i*3+1] = c.g 
    colors[i*3+2] = c.b 
  })
  const geom = new BufferGeometry().setFromPoints(points)
  geom.setAttribute('color', new BufferAttribute(colors, 3, true))
  return geom
}
const material = new PointsMaterial({size:0.1, vertexColors:true, color:0xffffff})
const points = new Points(createPoints(), material)
scene.add(points)
```
<img src="https://pro-developer.oss-cn-shanghai.aliyuncs.com/74/img/1677634333433_Screenshot 2023-03-01 at 09.31.56.png" width="60%">  

这里，我们显示了15000个点。令人惊喜的是，就算是一百万个点，它也能很丝滑地渲染。

# 高级Mesh和Geometry
## Geometry Grouping and Merging
当你从一个用了多种material的geometry创建mesh时,three.js会为你创建一个group。实际上，这已经是一个mesh group了。  
当你把子对象加到父对象的group里，你对父对象的平移、伸缩、旋转也会对子对象生效。同时你也可以修改单个geometry。
```javascript
const size = 1
const amount = 5000
const range = 20
const group = new Group()
const mat = new MeshNormalMaterial()
mat.blending = NormalBlending
mat.opacity = 0.1
mat.transparent = true 
for (let i=0; i<amount; i++) {
  const x = Math.random()*range - range/2
  const y = Math.random()*range - range/2
  const z = Math.random()*range - range/2
  const g = new BoxGeometry(size,size,size)
  const m = new Mesh(g,mat)
  m.position.set(x,y,z)
  group.add(m)
}
```
<img src="https://pro-developer.oss-cn-shanghai.aliyuncs.com/74/img/1677634477869_Screenshot 2023-03-01 at 09.34.24.png" width="60%">  

## 从外部资源加载geometry
有了grouping和merging方法，你可以在threejs提供的基础geometry上，创建大而复杂的geometry。但一般更高级的几何模型都是由专业的建模软件生产，而threejs负责加载和渲染。只是有些模型格式的特性可能不被three.js支持，可能texture或material会出问题。three.js支持的最好的是glTF格式。three.js支持的格式有:  
> AMF, 3DM, 3MF, COLLADA, Draco, GCode, glTF, IFC, JSON, KMZ, LDraw, LWO, NRRD, OBJ/MTL, PCD, PDB, PLY, PRWM, STL, SVG, 3DS, TILT, VOX, VRML, VTK, XYZ  

## 保存和加载JSON格式
three.js把mesh导出成JSON
```javascript
const asJson = mesh.toJSON()
```
```json
{
  "metadata": {
    "generator": "Object3D.toJSON",
    "type": "Object",
    "version": 4.5
  },
  "geometries": [{
    "uuid": "4eedb69d-e0f9-479e-bae1-11fe9133c8a8",
    "depth": 1,
    "depthSegments": 10,
    "height": 1,
    "heightSegments": 10,
    "width": 1,
    "widthSegments": 10,
    "type": "BoxGeometry"
  }],
  "materials":[
    {
      "color": 7829367,
      "colorWrite": true,
      "depthFunc": 3,
      "depthTest": true,
      "depthWrite": true,
      "reflectivity": 1,
      "refractionRatio": 0.98,
      "stencilFail": 7680,
      "stencilFunc": 519,
      "stencilFuncMask": 255,
      "stencilRef": 0,
      "stencilWrite": false,
      "stencilWriteMask": 255,
      "stencilZFail": 7680,
      "stencilZPass": 7680,
      "type": "MeshBasicMaterial",
      "uuid": "6149c964-a8ec-48e7-b5fb-cf5892bd4b76"
    },{
      "color": 16711680,
      "colorWrite": true,
      "depthFunc": 3,
      "depthTest": true,
      "depthWrite": true,
      "reflectivity": 1,
      "refractionRatio": 0.98,
      "stencilFail": 7680,
      "stencilFunc": 519,
      "stencilFuncMask": 255,
      "stencilRef": 0,
      "stencilWrite": false,
      "stencilWriteMask": 255,
      "stencilZFail": 7680,
      "stencilZPass": 7680,
      "type": "MeshBasicMaterial",
      "uuid": "b9d23e72-f4c2-437e-8dd1-e28ae4b19906"
    }
  ],
  "object": {
    "geometry": "4eedb69d-e0f9-479e-bae1-11fe9133c8a8",
    "layers": 1,
    "material": [
      "6149c964-a8ec-48e7-b5fb-cf5892bd4b76",
      "b9d23e72-f4c2-437e-8dd1-e28ae4b19906"
    ],
    "matrix": [1,0,0,0,0,1,0,0,0,0,1,0,0,0,0,1]
  }
}
```
加载JSON
```javascript
const structure = JSON.parse(jsonstr)
const loader = new THREE.ObjectLoader()
const mesh = loader.parse(structure)
mesh.material.color = new THREE.Color(0xff0000)
scene.add(mesh)
```
## 导入其他3D文件格式
### OBJ和MTL
通常OBJ定义geometry,
```
v -0.032442 0.010796 0.025935
v -0.028519 0.013697 0.026201
v -0.029086 0.014533 0.021409
usemtl Material 
s   1
f   2731    2735 2736 2732
f   2732    2736 3043 3044
```
MTL定义material
```
newmtl Material
Ns  56.862745
Ka  0.000000    0.000000    0.000000
Kd  0.360725    0.227524    0.127497
Ks  0.010000    0.010000    0.010000
Ni  1.000000
d 1.000000
illum 2
```
```javascript
import { OBJLoader } from 'three/addons/loaders/OBJLoader'

new OBJLoader().loadAsync('/assets/models/baymax/Bigmax_White_OBJ.obj').then((model) => {
  model.scale.set(0.05, 0.05, 0.05)
  model.translateY(-1)
  visitChildren(model, (child) => {
    child.receiveShadow = true
    child.castShadow = true
  })
  return model
})
```
### glTF
```javascript
const visitChildren = (object, fn) => {
  if(object.children && object.children.length > 0) {
    for (const child of object.children) {
      visitChildren(child, fn)
    }
  }else {
    fn(object)
  }
}
const loader = new GLTFLoader()
loader.loadAsync('models/sea_house/scene.gltf').then((structure) => {
  structure.scene.scale.setScalar(0.2, 0.2, 0.2)
  visitChildren(structure.scene, (child) => {
    if (child.material) {
      child.material.depthWrite = true 
    }
  })
  console.log(structure.scene)
  // structure.scene.children[0].material.depthWrite = true
  scene.add(structure.scene)
})
```
### point cloud of PLY
```javascript
const texture = new THREE.TextureLoader().load('/assets/textures/particles/glow.png')
const material = new THREE.PointsMaterial({
  size: 0.15,
  vertexColors: false,
  color: 0xffffff,
  map: texture,
  depthWrite: false,
  opacity: 0.1,
  transparent: true,
  blending: THREE.AdditiveBlending 
})
new PLYLoader().loadAsync('/assets/models/carcloud/carcloud.ply').then((model) => {
  const points = new THREE.Points(model, material)
  points.scale.set(0.7, 0.7, 0.7)
  scene.add(points)
})
```
更多示例请参考之前的文档 [《three.js初体验》](https://dev.yijianar.com/open-doc?fileId=818886690)

# 移动相机

# 纹理加载

# 后处理

