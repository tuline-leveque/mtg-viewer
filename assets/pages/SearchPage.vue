<script setup>
import {ref, watch} from 'vue';
import {searchCards} from '../services/cardService';

const searchQuery = ref('');
const cards = ref([]);
const loading = ref(false);

async function search(name) {
  if (name.length < 3) {
    cards.value = [];
    return;
  }
  loading.value = true;
  cards.value = await searchCards(name);
  loading.value = false;
}

watch(searchQuery, (newVal) => search(newVal));
</script>

<template>
  <div>
    <h1>Rechercher une Carte</h1>
    <input v-model="searchQuery" type="text" placeholder="Entrez le nom d'une carte (min 3 caractères)"
           class="search-input"/>
  </div>

  <div class="card-list">
    <div v-if="loading">Loading...</div>
    <div v-else>
      <div class="card" v-for="c in cards" :key="c.uuid">
        <router-link :to="{ name: 'get-card', params: { uuid: c.uuid } }">
          {{ c.name }} - {{ c.uuid }}
        </router-link>
      </div>
    </div>
  </div>
</template>
