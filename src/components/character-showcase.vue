<script setup>
import axios from 'axios'
import { ref, reactive } from 'vue'
import characterCard from './character-card.vue'
import Pagination from './pagination.vue'

const props = defineProps({
  modalVisible: Boolean
})

const emits = defineEmits(['changeModalVisibility'])

const characters = ref([])
const info = reactive({})
const episodeLinks = ref([])
const episodeNames = ref([])
const currentPage = ref(1)
const maxPages = ref(0)
const errorAccured = ref(false)

const getCharacterData = async () => {
  try {
    const { data } = await fetchCharacters()

    characters.value = data.results
    info.value = data.info
    maxPages.value = info.value.pages

    characters.value.forEach((character) => {
      episodeLinks.value.push(character.episode[0])
    })

    fetchEpisodeNames(episodeLinks.value)

    characters.value.firstSeen = episodeNames.value
  } catch (err) {
    if (err.response.status === 404) {
      currentPage.value = 1
      filterParameters.name = ''
      searhQuerry.value = ''
      errorAccured.value = true
      emits('changeModalVisibility')
      getCharacterData()
    }
    console.log(err)
  }
}

const fetchCharacters = async () => {
  if (filterParameters.name && filterParameters.status) {
    return axios.get(
      `https://rickandmortyapi.com/api/character/?page=${currentPage.value}&name=${filterParameters.name}&status=${filterParameters.status}`
    )
  } else if (filterParameters.name) {
    return axios.get(
      `https://rickandmortyapi.com/api/character/?page=${currentPage.value}&name=${filterParameters.name}`
    )
  } else if (filterParameters.status) {
    return axios.get(
      `https://rickandmortyapi.com/api/character/?page=${currentPage.value}&status=${filterParameters.status}`
    )
  }
  return axios.get(`https://rickandmortyapi.com/api/character/?page=${currentPage.value}`)
}

const fetchEpisodeNames = (episodes) => {
  try {
    episodes.forEach(async (episode) => {
      const { data } = await axios.get(episode)
      episodeNames.value.push(data.name)
    })
  } catch (err) {
    console.log(err)
  }
}

const statusOption = ref('')
const searhQuerry = ref('')
const filterParameters = reactive({
  status: '',
  name: ''
})

const updateFilters = (filterOption) => {
  if (filterOption === 'alive' || filterOption === 'dead' || filterOption === 'unknown') {
    filterParameters.status = filterOption
  } else {
    filterParameters.name = filterOption.toLowerCase()
  }
}

const goToFirstPage = () => {
  currentPage.value = 1
  getCharacterData()
}

const goToLastPage = () => {
  currentPage.value = info.value.pages
  getCharacterData()
}

const goToClickedPage = (page) => {
  currentPage.value = page
  getCharacterData()
}

getCharacterData()
</script>

<template>
  <main>
    <section class="filterWrapper">
      <input
        class="filterInput"
        type="text"
        v-model.lazy="searhQuerry"
        @blur="updateFilters(searhQuerry)"
        placeholder="Поиск персонажа"
      />
      <select
        v-model.lazy="statusOption"
        @change="updateFilters(statusOption)"
        class="filterSelect"
      >
        <option disabled selected value="">Отфильтровать...</option>
        <option value="alive">Сейчас жив</option>
        <option value="dead">Мертв</option>
        <option value="unknown">Статус неизвестен</option>
      </select>
      <button @click="getCharacterData" class="filterButton">Применить фильтры</button>
    </section>
    <section>
      <div class="characterWrapper">
        <character-card
          v-for="(person, index) in characters"
          :key="person.id"
          :name="person.name"
          :status="person.status"
          :img="person.image"
          :species="person.species"
          :location="person.location.name"
          :first-seen="characters.firstSeen[index]"
        />
      </div>
      <Pagination
        @go-to-first-page="goToFirstPage()"
        @go-to-last-page="goToLastPage()"
        @go-to-clicked-page="goToClickedPage"
        :max-pages="maxPages"
        :currentPage="currentPage"
      />
    </section>
  </main>
</template>
