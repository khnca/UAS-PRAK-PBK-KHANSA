<template>
  <header class="sticky top-0 bg-gradient-to-r from-weather-primary to-weather-secondary shadow-lg">
    <nav class="container flex flex-col sm:flex-row items-center justify-between gap-4 text-white py-6">
      <RouterLink :to="{ name: 'home' }">
        <div class="flex items-center gap-3">
          <i class="fa-solid fa-sun text-4xl"></i>
          <p class="text-2xl font-bold tracking-wide text-white">The Local Weather</p>
        </div>
      </RouterLink>

      <div class="flex gap-3 flex-1 justify-end">
        <RouterLink :to="{ name: 'Pert1' }">
          <button class="text-xl hover:text-weather-secondary duration-150">Pertemuan 1</button>
        </RouterLink>
        <i class="fa-solid fa-circle-info text-xl hover:text-weather-secondary duration-150 cursor-pointer" @click="toggleModal"></i>
        <i class="fa-solid fa-plus text-xl hover:text-weather-secondary duration-150 cursor-pointer" @click="addCity" v-if="route.query"></i>
      </div>

      <div class="flex gap-3 flex-1 justify-end">
        <RouterLink :to="{ name: 'Pert2' }">
          <button class="text-xl hover:text-weather-secondary duration-150">Pertemuan 2</button>
        </RouterLink>
        <i class="fa-solid fa-circle-info text-xl hover:text-weather-secondary duration-150 cursor-pointer" @click="toggleModal"></i>
        <i class="fa-solid fa-plus text-xl hover:text-weather-secondary duration-150 cursor-pointer" @click="addCity" v-if="route.query"></i>
      </div>

      <div class="flex gap-3 flex-1 justify-end">
        <RouterLink :to="{ name: 'Pert3' }">
          <button class="text-xl hover:text-weather-secondary duration-150">Pertemuan 3</button>
        </RouterLink>
        <i class="fa-solid fa-circle-info text-xl hover:text-weather-secondary duration-150 cursor-pointer" @click="toggleModal"></i>
        <i class="fa-solid fa-plus text-xl hover:text-weather-secondary duration-150 cursor-pointer" @click="addCity" v-if="route.query"></i>
      </div>

      <div class="flex gap-3 flex-1 justify-end">
        <RouterLink :to="{ name: 'Pert4' }">
          <button class="text-xl hover:text-weather-secondary duration-150">Pertemuan 4</button>
        </RouterLink>
        <i class="fa-solid fa-circle-info text-xl hover:text-weather-secondary duration-150 cursor-pointer" @click="toggleModal"></i>
        <i class="fa-solid fa-plus text-xl hover:text-weather-secondary duration-150 cursor-pointer" @click="addCity" v-if="route.query"></i>
      </div>

      <div class="flex gap-3 flex-1 justify-end">
        <RouterLink :to="{ name: 'Pert5' }">
          <button class="text-xl hover:text-weather-secondary duration-150">Pertemuan 5</button>
        </RouterLink>
        <i class="fa-solid fa-circle-info text-xl hover:text-weather-secondary duration-150 cursor-pointer" @click="toggleModal"></i>
        <i class="fa-solid fa-plus text-xl hover:text-weather-secondary duration-150 cursor-pointer" @click="addCity" v-if="route.query"></i>
      </div>

      <div class="flex gap-3 flex-1 justify-end">
        <RouterLink :to="{ name: 'Pert6' }">
          <button class="text-xl hover:text-weather-secondary duration-150">Pertemuan 6</button>
        </RouterLink>
        <i class="fa-solid fa-circle-info text-xl hover:text-weather-secondary duration-150 cursor-pointer" @click="toggleModal"></i>
        <i class="fa-solid fa-plus text-xl hover:text-weather-secondary duration-150 cursor-pointer" @click="addCity" v-if="route.query"></i>
      </div>


      <BaseModal :modalActive="modalActive" @close-modal="toggleModal">
        <div class="text-black">
          <h1 class="text-2xl mb-1">About:</h1>
          <p class="mb-4">
            The Local Weather allows you to track the current and future weather of cities of your choosing.
          </p>
          <h2 class="text-2xl">How it works:</h2>
          <ol class="list-decimal list-inside mb-4">
            <li>Search for your city by entering the name into the search bar.</li>
            <li>Select a city within the results, this will take you to the current weather for your selection.</li>
            <li>Track the city by clicking on the "+" icon in the top right. This will save the city to view at a later time on the home page.</li>
          </ol>

          <h2 class="text-2xl">Removing a city</h2>
          <p>
            If you no longer wish to track a city, simply select the city within the home page. At the bottom of the page, there will be an option to delete the city.
          </p>
        </div>
      </BaseModal>
    </nav>
  </header>
</template>

<script setup>
import { RouterLink, useRoute, useRouter } from "vue-router";
import { uid } from "uid";
import { ref } from "vue";
import BaseModal from "./BaseModal.vue";

const savedCities = ref([]);
const route = useRoute();
const router = useRouter();

const addCity = () => {
  if (localStorage.getItem("savedCities")) {
    savedCities.value = JSON.parse(localStorage.getItem("savedCities"));
  }

  const locationObj = {
    id: uid(),
    state: route.params.state,
    city: route.params.city,
    coords: {
      lat: route.query.lat,
      lng: route.query.lng,
    },
  };

  savedCities.value.push(locationObj);
  localStorage.setItem("savedCities", JSON.stringify(savedCities.value));

  let query = { ...route.query };
  delete query.preview;
  query.id = locationObj.id;
  router.replace({ query });
};

const modalActive = ref(false);

const toggleModal = () => {
  modalActive.value = !modalActive.value;
};
</script>

<style scoped>
nav.container {
  max-width: 1200px; /* Lebar maksimum kontainer nav */
  margin: 0 auto; /* Posisi tengah */
}

.fa-sun {
  color: #fff; /* Warna ikon matahari */
}

.fa-circle-info,
.fa-plus {
  color: #fff; /* Warna ikon informasi dan tambah */
  transition: color 0.2s; /* Transisi warna saat hover */
}

.fa-circle-info:hover,
.fa-plus:hover {
  color: #a0aec0; /* Warna ikon saat dihover */
}

.BaseModal {
  z-index: 999; /* Menempatkan modal di atas konten lain */
}

.text-2xl {
  font-size: 2rem; /* Ukuran teks */
  line-height: 1.5; /* Jarak antar baris */
  text-align: center; /* Posisi teks di tengah */
  color: #fff; /* Warna teks putih */
}

.font-bold {
  font-weight: 700; /* Ketebalan teks tebal */
}

.tracking-wide {
  letter-spacing: 1px; /* Spasi antar huruf */
}

.bg-gradient-to-r {
  background-image: linear-gradient(to right, #FFE4C4, #E9967A); /* Gradient background */
}
</style>
