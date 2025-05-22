<template>
  <div ref="container" class="model-viewer">
    <!-- Balões explicativos -->
    <div
      v-for="(tip, index) in tips"
      :key="index"
      class="tooltip"
      :style="{ top: tip.screenY + 'px', left: tip.screenX + 'px' }"
    >
      {{ tip.text }}
    </div>
  </div>
</template>

<script>
import * as THREE from 'three'
import { GLTFLoader } from 'three/examples/jsm/loaders/GLTFLoader'
import { OrbitControls } from 'three/examples/jsm/controls/OrbitControls'

export default {
  name: 'BrainModel',
  data() {
    return {
      tips: [
        { position: new THREE.Vector3(5, 8, 0), text: 'Córtex Pré-Frontal: Tomada de decisões' },
        { position: new THREE.Vector3(-1, 10, 0.5), text: 'Hipocampo: Memória e aprendizado' },
        { position: new THREE.Vector3(-5, 8, -1), text: 'Amígdala: Processamento emocional' },
      ],
    }
  },
  mounted() {
    this.initThree()
  },
  methods: {
    initThree() {
      const container = this.$refs.container

      const scene = new THREE.Scene()
      scene.background = new THREE.Color(0xf5f5f5)

      const camera = new THREE.PerspectiveCamera(
        45,
        container.clientWidth / container.clientHeight,
        0.1,
        1000
      )
      camera.position.set(0, 20, 20)

      const renderer = new THREE.WebGLRenderer({ antialias: true })
      renderer.setSize(container.clientWidth, container.clientHeight)
      container.appendChild(renderer.domElement)

      const light = new THREE.HemisphereLight(0xffffff, 0x444444, 1)
      light.position.set(0, 1, 0)
      scene.add(light)

      const controls = new OrbitControls(camera, renderer.domElement)
      controls.enableDamping = true

      const loader = new GLTFLoader()
      const modelUrl = 'https://cdn.glitch.global/f38f07fb-03af-4708-a977-5d5a9aab38e0/brain_in_head.glb?v=1747922230742'

      loader.load(
        modelUrl,
        (gltf) => {
          gltf.scene.scale.set(1, 1, 1)
          scene.add(gltf.scene)
          animate()
        },
        undefined,
        (error) => {
          console.error('Erro ao carregar GLB:', error)
        }
      )

      // função para converter posição 3D para 2D na tela
      const toScreenPosition = (obj, camera) => {
        const vector = obj.clone()
        vector.project(camera)

        const x = (vector.x + 1) / 2 * container.clientWidth
        const y = (-vector.y + 1) / 2 * container.clientHeight
        return { x, y }
      }

      const animate = () => {
        requestAnimationFrame(animate)
        controls.update()
        renderer.render(scene, camera)

        // Atualiza posições dos balões
        this.tips.forEach((tip) => {
          const pos2d = toScreenPosition(tip.position, camera)
          tip.screenX = pos2d.x
          tip.screenY = pos2d.y
        })
      }

      window.addEventListener('resize', () => {
        camera.aspect = container.clientWidth / container.clientHeight
        camera.updateProjectionMatrix()
        renderer.setSize(container.clientWidth, container.clientHeight)
      })
    }
  }
}
</script>

<style scoped>
.model-viewer {
  position: fixed;
  top: 0;
  left: 0;
  width: 100vw;
  height: 100vh;
  background-color: #000;
  z-index: 0;
  overflow: hidden;
}

/* Balão estilizado */
.tooltip {
  position: absolute;
  background: rgba(255, 255, 255, 0.85);
  color: #333;
  padding: 6px 10px;
  border-radius: 8px;
  font-size: 14px;
  pointer-events: none;
  user-select: none;
  transform: translate(-50%, -100%);
  white-space: nowrap;
  box-shadow: 0 0 6px rgba(0,0,0,0.2);
}
</style>
