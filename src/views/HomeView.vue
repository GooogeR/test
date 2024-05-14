<template>
  <div>
    <h1>Персонажи Рика и Морти</h1>
    <div>
      <input v-model="filters.name" placeholder="Сортировка по имени" />
      <select v-model="filters.status">
        <option value="">Все</option>
        <option value="Alive">Живые</option>
        <option value="Dead">Мертвые</option>
        <option value="unknown">Неизвестно</option>
      </select>
      <button @click="applyFilters">Принять</button>
    </div>
    <div>
      <button @click="prevPage" :disabled="page === 1">Назад</button>
      <button @click="nextPage" :disabled="!hasNextPage">Дальше</button>
    </div>
    <div class="characters">
      <CharacterCard v-for="character in characters" :key="character.id" :character="character" />
    </div>
  </div>
</template>

<script>
import { ref, onMounted } from 'vue';
import axios from 'axios';
import CharacterCard from '../components/CharacterCard.vue';

export default {
  components: {
    CharacterCard
  },
  setup() {
    const characters = ref([]);
    const page = ref(1);
    const hasNextPage = ref(false);
    const filters = ref({
      name: '',
      status: ''
    });

    const fetchCharacters = async () => {
      const response = await axios.get(`https://rickandmortyapi.com/api/character`, {
        params: {
          page: page.value,
          name: filters.value.name,
          status: filters.value.status
        }
      });
      characters.value = response.data.results;
      hasNextPage.value = response.data.info.next !== null;
    };

    const applyFilters = () => {
      page.value = 1;
      fetchCharacters();
    };

    const nextPage = () => {
      if (hasNextPage.value) {
        page.value++;
        fetchCharacters();
      }
    };

    const prevPage = () => {
      if (page.value > 1) {
        page.value--;
        fetchCharacters();
      }
    };

    onMounted(fetchCharacters);

    return {
      characters,
      page,
      hasNextPage,
      filters,
      applyFilters,
      nextPage,
      prevPage
    };
  }
};
</script>