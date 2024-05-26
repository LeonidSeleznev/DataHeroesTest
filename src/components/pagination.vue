<script setup>
import axios from 'axios'
import { ref } from 'vue'

const props = defineProps({
  currentPage: Number,
  maxPages: Number
})

const emits = defineEmits(['goToFirstPage', 'goToLastPage', 'goToClickedPage'])

const prevPage = ref(0)
const nextPage = ref(0)

const clickablePages = ref([])
const fetchPages = async () => {
  try {
    const { data } = await axios.get(
      `https://rickandmortyapi.com/api/character/?${props.currentPage}`
    )
    setPages(props.currentPage)
  } catch (err) {
    console.log(err)
  }
}

const setPages = (currentPage) => {
  prevPage.value = currentPage - 1
  nextPage.value = currentPage + 1
  clickablePages.value = [prevPage.value, currentPage, nextPage.value]
}

const pickFlexStyle = (maxPages) => {
  if (maxPages < 3) {
    return 'pagination__centered'
  } else {
    return 'pagination'
  }
}

fetchPages()
</script>

<template>
  <div class="paginationWrapper">
    <ul :class="pickFlexStyle(props.maxPages)">
      <li @click="emits('goToFirstPage')" class="pagination__el" v-if="props.currentPage > 2">1</li>

      <li class="pagination__divider" v-if="props.currentPage > 2">...</li>

      <li
        v-if="props.currentPage == props.maxPages && props.maxPages >= 3"
        @click="() => emits('goToClickedPage', props.currentPage - 2)"
        class="pagination__el"
      >
        {{ props.currentPage - 2 }}
      </li>

      <li
        v-if="props.currentPage !== 1"
        @click="() => emits('goToClickedPage', props.currentPage - 1)"
        class="pagination__el"
      >
        {{ props.currentPage - 1 }}
      </li>

      <li disabled class="pagination__el__active">{{ props.currentPage }}</li>

      <li
        v-if="props.currentPage !== props.maxPages"
        @click="() => emits('goToClickedPage', props.currentPage + 1)"
        class="pagination__el"
      >
        {{ props.currentPage + 1 }}
      </li>

      <li
        v-if="props.currentPage === 1 && props.maxPages >= 3"
        @click="() => emits('goToClickedPage', props.currentPage + 2)"
        class="pagination__el"
      >
        {{ props.currentPage + 2 }}
      </li>

      <li v-if="props.currentPage <= props.maxPages - 2" class="pagination__divider">...</li>

      <li
        v-if="props.currentPage <= props.maxPages - 2"
        @click="() => emits('goToLastPage', props.maxPages)"
        class="pagination__el"
      >
        {{ props.maxPages }}
      </li>
    </ul>
  </div>
</template>
