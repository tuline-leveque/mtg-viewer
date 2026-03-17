<script setup>
import { ref, watch } from 'vue';
import { searchCard } from '../services/cardService';

const searchQuery = ref('');
const cards = ref([]);
const loading = ref(false);

async function performSearch(name) {
  if (name.length < 3) {
    cards.value = [];
    return;
  }

  loading.value = true;
  try {
    cards.value = await searchCard(name);
  } catch (error) {
    console.error("Erreur recherche:", error);
  } finally {
    loading.value = false;
  }
}

watch(searchQuery, (newVal) => {
  performSearch(newVal);
});
</script>

<template>
  <div>
    <h1>Rechercher une Carte</h1>

    <input
        v-model="searchQuery"
        placeholder="Tapez le nom d'une carte..."
        class="search-field"
    />
  </div>

  <div class="card-list">
    <div v-if="loading">Chargement...</div>
    <div v-else>
      <div class="card" v-for="c in cards" :key="c.uuid">
        <router-link :to="{ name: 'get-card', params: { uuid: c.uuid } }">
          {{ c.name }} - {{ c.uuid }}
        </router-link>
      </div>

      <div v-if="searchQuery.length >= 3 && cards.length === 0 && !loading">
        Aucune carte trouvée.
      </div>
    </div>
  </div>
</template>