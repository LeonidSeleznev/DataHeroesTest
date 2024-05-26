<script setup>
import headerComp from './components/header-comp.vue'
import characterShowcase from './components/character-showcase.vue'
import Modal from './components/modal.vue'
import { ref } from 'vue'

const modalVisible = ref(false)

const changeModalVisibility = () => {
  modalVisible.value = !modalVisible.value
}
</script>

<template>
  <div class="containter">
    <transition name="fade">
      <Modal :modal-visible="modalVisible" @change-modal-visibility="changeModalVisibility" />
    </transition>

    <header-comp />
    <character-showcase
      :modal-visible="modalVisible"
      @change-modal-visibility="changeModalVisibility"
    />
  </div>
</template>
