<template>
  <div class="pageB">
    <div id="pageBMap" />
  </div>
</template>

<script>
import * as THREE from 'three'
import { GLTFLoader } from 'three/examples/jsm/loaders/GLTFLoader'
import * as maptalks from 'maptalks'
import { ThreeLayer } from 'maptalks.three'
export default {
  data () {
    return {
      b: null
    }
  },
  beforeCreate () {
    console.log('pageB beforeCreate')
  },
  created () {
    console.log('pageB created')
  },
  beforeMount () {
    console.log('pageB beforeMount')
  },
  mounted () {
    console.log('pageB mounted')
    const lon = 114.07
    const lat = 22.62
    let map = new maptalks.Map('pageBMap', {
      center: [lon, lat],
      zoom: 17,
      pitch: 45,
      dragPitch: true,
      dragRotatePitch: true,
      minZoom: 1,
      maxZoom: 19,
      spatialReference: {
        projection: 'baidu'
      },
      baseLayer: new maptalks.TileLayer('base', {
        urlTemplate:
          'http://api{s}.map.bdimg.com/customimage/tile?&x={x}&y={y}&z={z}&scale=1&customid=midnight',
        subdomains: [0, 1, 2],
        attribution: '&copy; <a href="http://map.baidu.com/">Baidu</a>'
      })
    })

    let threeLayer = new ThreeLayer('t', {
      forceRenderOnMoving: true,
      forceRenderOnZooming: true,
      forceRenderOnRotating: true
    })

    threeLayer.prepareToDraw = function (gl, scene, camera) {
      scene.add(new THREE.AmbientLight(0xffffff))
      let loader = new GLTFLoader()

      loader.load('/static/mode/scene.gltf', gltf => {
        let v = threeLayer.coordinateToVector3(
          new maptalks.Coordinate(lon, lat)
        )
        gltf.scene.position.set(v.x + 0.5, v.y + 2.5, 0)
        gltf.scene.rotation.x = Math.PI / 2
        scene.add(gltf.scene)
        threeLayer.renderScene()
      })
    }

    threeLayer.addTo(map)
  },
  activated () {
    console.log('pageB activated')
  },
  deactivated () {
    console.log('pageB deactivated')
  },
  beforeUpdate () {
    console.log('pageB beforeUpdate')
  },
  updated () {
    console.log('pageB updated')
  },
  beforeDestroy () {
    console.log('pageB beforeDestroy')
  },
  destroyed () {
    console.log('pageB destroyed')
  }
}
</script>

<style lang="less" scoped>
.pageB {
  width: 100%;
  height: 100%;
  #pageBMap {
    width: 100%;
    height: 100%;
  }
}
</style>
