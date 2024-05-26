<script setup>
const props = defineProps({
  modalVisible: Boolean
})

const emits = defineEmits(['changeModalVisibility'])
</script>

<template>
  <div v-if="props.modalVisible" class="modal" @click.self="emits('changeModalVisibility')">
    <div class="modal__content">
      <button class="modal__button" @click.self="emits('changeModalVisibility')">Х</button>
      <h2 class="modal__header">Произошла ошибка</h2>
      <p class="modal__paragraph">При фильтрации персонажей, произошла ошибка.</p>
      <p class="modal__paragraph">Пожалуйста, попробуйте снова!</p>
    </div>
  </div>
</template>
