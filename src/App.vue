<template>
  <v-app>
    <v-main>
      <v-container class="py-12">
        <h1 class="text-center mb-6">🧠 Visualização 3D do Cérebro</h1>
        <BrainModel />
      </v-container>
    </v-main>
  </v-app>
</template>

<script>
import BrainModel from './components/BrainModel.vue'

export default {
  components: {
    BrainModel
  }
}


window.addEventListener('resize', () => {
  camera.aspect = container.clientWidth / container.clientHeight
  camera.updateProjectionMatrix()
  renderer.setSize(container.clientWidth, container.clientHeight)
})

</script>


