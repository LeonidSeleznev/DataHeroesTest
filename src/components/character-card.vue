<script setup>
const props = defineProps({
  name: String,
  status: String,
  img: String,
  species: String,
  location: String,
  firstSeen: String,
})

const pickStatusColor = (status) => {
  if (status == 'Dead') {
    return 'status__dead'
  }
  if (status == 'unknown') {
    return 'status__unknown'
  }
  return 'status__alive'
}
</script>

<template>
  <article class="characterCard__wrapper">
    <div class="characterCard__imgWrapper">
      <img :src="img" alt="" />
    </div>
    <div class="characterCard__contentWrapper">
      <div class="section">
        <h3 class="characterName">{{ name }}</h3>
        <span class="status">
          <span class="status__icon" :class="pickStatusColor(status)"></span>
          {{ status }} - {{ species }}
        </span>
      </div>
      <div class="section">
        <p class="location-text">Last known location:</p>
        <p class="last-location">{{ location }}</p>
      </div>
      <div class="section">
        <p class="seen-text">First seen in:</p>
        <p class="first-seen">{{ firstSeen }}</p>
      </div>
    </div>
  </article>
</template>
