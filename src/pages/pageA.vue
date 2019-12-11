<template>
  <div class="pageA">
    <div id="scene-wrapper" />
  </div>
</template>

<script>
import * as THREE from 'three'
import { GLTFLoader } from 'three/examples/jsm/loaders/GLTFLoader'
// import GLTFLoader from 'three-gltf-loader'
import { Interaction } from 'three.interaction'
import { OBJLoader } from 'three-obj-mtl-loader'
// import OrbitControls from 'three-orbitcontrols'
window.THREE = THREE
require('three-orbitcontrols')
let scene = null
let camera = null
let width = null
let height = null
let cameraGroup = null
let renderer = null
let controls = null
let ambientLight = null
let keyLight = null
let keyLightSphere = null
let axis = null
let roadSignGeometrys = []
let modeArray = [] // 用于存放读取的外部模型
let cameraArray = [] // 用于存放读取的外部模型
window.cameraArray = cameraArray
let ballArray = [] // 用于存放读取的外部模型
let phase = 0
const LINE_COLOR = 0x2283b8
const CAMERA_ERROR_COLOR = 0xff0000
const CAMERA_WARNING_COLOR = 0xff8e5b
const CAMERA_TIP_COLOR = 0xffff03
const CAMERA_NORMAL_COLOR = 0x0eb9d0

THREE.SceneUtils1 = {
  createMultiMaterialObject: function (geometry, materials) {
    var group = new THREE.Group()

    for (var i = 0, l = materials.length; i < l; i++) {
      group.add(new THREE.Mesh(geometry, materials[i]))
    }

    return group
  },

  detach: function (child, parent, scene) {
    child.applyMatrix(parent.matrixWorld)
    parent.remove(child)
    scene.add(child)
  },

  attach: function (child, scene, parent) {
    child.applyMatrix(new THREE.Matrix4().getInverse(parent.matrixWorld))

    scene.remove(child)
    parent.add(child)
  }
}

function init () {
  initScene()
  initCamera()
  initRenderer()
  initControls()
  initInteraction()
  initLight()
  initAxis()
  loaderMode()
  animation()
  window.addEventListener('resize', onWindowResize, false)
}
function initScene () {
  scene = new THREE.Scene()
}
function initCamera () {
  let sceneWrapper = document.getElementById('scene-wrapper')
  width = sceneWrapper.clientWidth
  height = sceneWrapper.clientHeight
  camera = new THREE.PerspectiveCamera(75, width / height, 0.1, 100000)
  camera.position.set(3, 3, -1)
  cameraGroup = new THREE.Group()
  scene.add(cameraGroup)
}
function initRenderer () {
  renderer = new THREE.WebGLRenderer({
    antialias: true,
    alpha: true
  })
  renderer.sortObjects = false
  renderer.setClearColor(0x000000, 0)
  renderer.setSize(width, height)
  document.getElementById('scene-wrapper').appendChild(renderer.domElement)
}
function initControls () {
  controls = new THREE.OrbitControls(camera, renderer.domElement)
  controls.addEventListener('change', () => {
    renderer.render(scene, camera)
  })
}
function initInteraction () {
  // eslint-disable-next-line no-new
  new Interaction(renderer, scene, camera)
}
function initLight () {
  ambientLight = new THREE.AmbientLight(0xffffff, 0.5)
  scene.add(ambientLight)
  keyLight = new THREE.SpotLight(0xffffff)
  keyLight.position.set(3000, 1000, 5000)
  keyLight.target.position.set(0, 0, 0)
  keyLightSphere = new THREE.Mesh(
    new THREE.SphereGeometry(5, 20, 20),
    new THREE.MeshBasicMaterial({ color: 0x00ffff })
  )
  keyLightSphere.position.copy(keyLight.position)
  scene.add(keyLightSphere)
  scene.add(keyLight)
}
function initAxis () {
  axis = new THREE.AxesHelper(10000)
  // scene.add(axis)
}
function loaderMode () {
  const loader = new GLTFLoader()
  loader.load('/static/mode/scene.gltf', gltf => {
    scene.add(gltf.scene)
  })
  // let objLoader = new OBJLoader()
  // objLoader.load('../../static/mode/palace2.obj', obj => {
  //   let group = obj
  //   group.traverse(child => {
  //     if (child instanceof THREE.Mesh) {
  //       let geometry = child.geometry
  //       geometry.computeVertexNormals()
  //       let mode = new Mode({ geometry, speed: 0.05, color: LINE_COLOR })
  //       modeArray.push(mode)
  //     }
  //   })
  //   let loadGroup = new THREE.Group()
  //   modeArray.forEach(mode => {
  //     loadGroup.add(mode.modeGroup)
  //   })

  //   loadRoadSign().then(function () {
  //     addRoadSign({
  //       id: '001',
  //       roadSignPosition: new THREE.Vector3(-500, 77, 900),
  //       ballPosition: new THREE.Vector3(-400, 0, 900),
  //       status: 0
  //     })
  //     addRoadSign({
  //       id: '002',
  //       roadSignPosition: new THREE.Vector3(-500, 77, 700),
  //       ballPosition: new THREE.Vector3(-400, 0, 700),
  //       status: 0
  //     })
  //     addRoadSign({
  //       id: '003',
  //       roadSignPosition: new THREE.Vector3(-100, 77, 800),
  //       status: 0
  //     })
  //     addRoadSign({
  //       id: '004',
  //       roadSignPosition: new THREE.Vector3(100, 77, 800),
  //       status: 0
  //     })
  //     addRoadSign({
  //       id: '005',
  //       roadSignPosition: new THREE.Vector3(-300, 77, 500),
  //       ballPosition: new THREE.Vector3(-400, 0, 500),
  //       status: 0
  //     })
  //     addRoadSign({
  //       id: '006',
  //       roadSignPosition: new THREE.Vector3(0, 77, 500),
  //       ballPosition: new THREE.Vector3(-0, 0, 500),
  //       status: 0
  //     })
  //     addRoadSign({
  //       id: '007',
  //       roadSignPosition: new THREE.Vector3(300, 77, 500),
  //       status: 0
  //     })
  //     addRoadSign({
  //       id: '008',
  //       roadSignPosition: new THREE.Vector3(-300, 77, 300),
  //       ballPosition: new THREE.Vector3(-400, 0, 300),
  //       status: 0
  //     })
  //     addRoadSign({
  //       id: '009',
  //       roadSignPosition: new THREE.Vector3(0, 77, 300),
  //       status: 0
  //     })
  //     addRoadSign({
  //       id: '010',
  //       roadSignPosition: new THREE.Vector3(300, 77, 300),
  //       status: 0
  //     })
  //     addRoadSign({
  //       id: '011',
  //       roadSignPosition: new THREE.Vector3(-500, 77, -250),
  //       ballPosition: new THREE.Vector3(-400, 0, -200),
  //       status: 0
  //     })
  //     addRoadSign({
  //       id: '012',
  //       roadSignPosition: new THREE.Vector3(0, 77, -250),
  //       ballPosition: new THREE.Vector3(0, 0, -200),
  //       status: 0
  //     })
  //     addRoadSign({
  //       id: '013',
  //       roadSignPosition: new THREE.Vector3(500, 77, -250),
  //       ballPosition: new THREE.Vector3(450, 0, -200),
  //       status: 0
  //     })
  //     addRoadSign({
  //       id: '014',
  //       roadSignPosition: new THREE.Vector3(-500, 77, -550),
  //       ballPosition: new THREE.Vector3(-400, 0, -600),
  //       status: 0
  //     })
  //     addRoadSign({
  //       id: '015',
  //       roadSignPosition: new THREE.Vector3(0, 77, -800),
  //       status: 0
  //     })
  //     addRoadSign({
  //       id: '016',
  //       roadSignPosition: new THREE.Vector3(500, 77, -550),
  //       ballPosition: new THREE.Vector3(450, 0, -600),
  //       status: 0
  //     })
  //   })

  //   loadGroup.position.set(0, 0, 0)
  //   scene.add(loadGroup)
  // })
}
function animation () {
  update()
  controls.update()
  renderer.render(scene, camera)
  scene.rotateY(0.002)
  requestAnimationFrame(animation)
}

function update () {
  phase += 0.05
  cameraGroup.rotateOnAxis(new THREE.Vector3(0, 1, 0).normalize(), 0.003)
  modeArray.forEach(mode => {
    mode && mode.update()
  })
}
function Mode ({ geometry, speed, isLight, color }) {
  this.speed = speed
  this.modeGroup = new THREE.Group()

  let vertexShader = `
                attribute float phase;
                uniform float time;
                varying float vPhase;
                void main(){
                    vPhase = phase+time;
                    gl_Position = projectionMatrix * modelViewMatrix * vec4(position, 1.0);
                }
            `
  let fragmentShader = `
                uniform vec3 color;
                varying float vPhase;
                void main(){
                    float alpha = abs(sin(vPhase));
                    gl_FragColor = vec4(color,alpha );
                }
            `
  this.uniforms = {
    time: {
      type: 'float',
      value: 0
    },
    color: {
      type: 'float',
      value: new THREE.Color(color)
    }
  }
  let attributes = {
    phase: {
      type: 'float',
      value: new Float32Array(geometry.attributes.position.count)
    }
  }
  let count = 0
  attributes.phase.value = attributes.phase.value.map(v => {
    count++
    return count % 4 < 2 ? Math.PI * 0.5 : 0
  })
  let frameShader = new THREE.ShaderMaterial({
    vertexShader,
    fragmentShader,
    uniforms: this.uniforms,
    transparent: true,
    wireframe: true
  })
  geometry.addAttribute(
    'phase',
    new THREE.BufferAttribute(attributes.phase.value, 1)
  )
  // let materials = [new THREE.MeshLambertMaterial({
  let materials = [
    new THREE.MeshPhongMaterial({
      color: LINE_COLOR,
      transparent: true,
      opacity: 0.5,
      depthWrite: false
    }),
    frameShader
  ]
  let mode = THREE.SceneUtils1.createMultiMaterialObject(geometry, materials)

  this.update = function () {
    this.uniforms.time.value += this.speed
  }
  this.modeGroup.add(mode)
}
/*
 * status 0:正常
 * status 1:提示
 * status 2:警告
 * status 3:异常
 * */
function addRoadSign ({ id, roadSignPosition, ballPosition, status }) {
  let color
  if (status === 0) {
    color = CAMERA_NORMAL_COLOR
  } else if (status === 1) {
    color = CAMERA_TIP_COLOR
  } else if (status === 2) {
    color = CAMERA_WARNING_COLOR
  } else {
    color = CAMERA_ERROR_COLOR
  }
  let groups = new THREE.Group()

  roadSignGeometrys.forEach(geometry => {
    let mesh = new THREE.Mesh(
      geometry,
      new THREE.MeshPhongMaterial({
        color: CAMERA_NORMAL_COLOR
      })
    )
    groups.add(mesh)
  })
  groups.position.copy(roadSignPosition)
  groups.scale.set(0.1, 0.1, 0.1)
  groups.name = 'camera' + id
  cameraArray.push(groups)
  addMouseTipaAndClick(groups, function () {})
  scene.add(groups)

  if (ballPosition) {
    let count = 50
    let r = 50
    // let ball = new THREE.Mesh(new THREE.CircleGeometry(50,50),new THREE.MeshLambertMaterial({
    let lineGeometry = new THREE.Geometry()
    for (let i = 0; i < count + 1; i++) {
      let angle = Math.PI * 2 * (i / count)
      let x = r * Math.sin(angle)
      let y = r * Math.cos(angle)
      lineGeometry.vertices.push(new THREE.Vector3(x, y, 0))
    }
    let ball = new THREE.Line(
      lineGeometry,
      new THREE.LineDashedMaterial({
        color,
        transparent: true,
        opacity: 1,
        depthTest: false,
        side: THREE.DoubleSide,
        dashSize: 10, // 短划线的大小
        gapSize: 10 // 短划线之间的距离
      })
    )
    ball.computeLineDistances()
    ball.position.copy(ballPosition)
    ball.rotation.x = -0.5 * Math.PI
    ball.name = 'ball' + id
    ballArray.push(ball)
    scene.add(ball)
  }
}
function addMouseTipaAndClick (group, fn) {
  group.on('click', e => {
    fn()
  })
  group.on('mouseover', e => {
    document.querySelector('.show').style.cursor = 'pointer'
  })
  group.on('mouseout', e => {
    document.querySelector('.show').style.cursor = 'default'
  })
}
function loadRoadSign () {
  let promise = new Promise((resolve, reject) => {
    let objLoader = new OBJLoader()
    objLoader.load('../../static/mode/geo2.obj', function (obj) {
      let group = obj
      group.children.forEach(child => {
        let geometry = child.geometry
        geometry.computeVertexNormals()
        roadSignGeometrys.push(geometry)
      })
      resolve()
    })
  })

  return promise
}
function onWindowResize () {
  let width = document.querySelector('.show').clientWidth
  let height = document.querySelector('.show').clientHeight
  camera.aspect = width / height
  camera.updateProjectionMatrix()

  renderer.setSize(width, height)
}

function startAnimation () {
  let errorNum = '012'
  let warningNum = '008'
  let tipNum = '006'

  // 错误
  let errorCmaera = cameraArray.filter(function (camera) {
    return camera.name === 'camera' + errorNum
  })
  errorCmaera[0].children.forEach(function (mesh) {
    mesh.material.color = new THREE.Color(CAMERA_ERROR_COLOR)
  })
  let errorBall = ballArray.filter(function (ball) {
    return ball.name === 'ball' + errorNum
  })
  errorBall[0].material.color = new THREE.Color(CAMERA_ERROR_COLOR)

  // 警告
  let warningCmaera = cameraArray.filter(function (camera) {
    return camera.name === 'camera' + warningNum
  })
  warningCmaera[0].children.forEach(function (mesh) {
    mesh.material.color = new THREE.Color(CAMERA_WARNING_COLOR)
  })
  let waningBall = ballArray.filter(function (ball) {
    return ball.name === 'ball' + warningNum
  })
  waningBall[0].material.color = new THREE.Color(CAMERA_WARNING_COLOR)

  // 提示
  let tipCmaera = cameraArray.filter(function (camera) {
    return camera.name === 'camera' + tipNum
  })
  tipCmaera[0].children.forEach(function (mesh) {
    mesh.material.color = new THREE.Color(CAMERA_TIP_COLOR)
  })
  let tipBall = ballArray.filter(function (ball) {
    return ball.name === 'ball' + tipNum
  })
  tipBall[0].material.color = new THREE.Color(CAMERA_TIP_COLOR)
}

// import PageAChild from '../components/pageA/PageAChild.vue'
export default {
  // components: {
  //   PageAChild
  // },
  data () {
    return {
      obj: {
        a: 1
      }
    }
  },
  beforeCreate () {
    console.log('pageA beforeCreate')
  },
  created () {
    console.log('pageA created')
  },
  beforeMount () {
    console.log('pageA beforeMount')
  },
  mounted () {
    console.log('pageA mounted')
    this.$nextTick(() => {
      init()
    })
    // setTimeout(() => {
    //   init()
    // }, 100)
    // const that = this
    // setTimeout(() => {
    //   startAnimation()
    // }, 15000)
  },
  activated () {
    console.log('pageA activated')
  },
  deactivated () {
    console.log('pageA deactivated')
  },
  beforeUpdate () {
    console.log('pageA beforeUpdate')
  },
  updated () {
    console.log('pageA updated')
  },
  beforeDestroy () {
    console.log('pageA beforeDestroy')
  },
  destroyed () {
    console.log('pageA destroyed')
  },
  errorCaptured (err, vm, info) {
    console.log('pageA errorCaptured')
    console.log('err', err)
    console.log('vm', vm)
    console.log('info', info)
    return false
  }
}
</script>

<style lang="less" scoped>
.pageA,
#scene-wrapper {
  width: 100%;
  height: 100%;
}
</style>
