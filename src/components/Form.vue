<script setup>
import { ref } from "vue";

const query = ref("");
const limit = ref("");
const gifs = ref([]);
const fetchGifs = async () => {
  const response = await fetch(
    `https://api.giphy.com/v1/gifs/search?api_key=${
      import.meta.env.VITE_GIPHY_API_KEY
    }&q=${query.value}&limit=${limit.value}&rating=g`,
    {
      method: "GET",
      headers: {
        "Content-Type": "application/json",
      },
    }
  );
  const data = await response.json();
  gifs.value = data.data;
  query.value = "";
  limit.value = "";
};

// fetchGifs();
</script>

<template>
  <div class="flex flex-col items-center -center h-screen">
    <!-- header-->
    <h1 class="text-3xl font-bold text-blue-300 text-center mt-10 mb-5">
      GIPHY MAKES SEARCH EASY...
    </h1>
    <!-- form/search-bar-->
    <form
      @submit.prevent="fetchGifs"
      class="flex items-center border border-gray-300 rounded-md pl-2 mb-5"
    >
      <input
        v-model="query"
        type="text"
        placeholder="Search for a GIF..."
        class="outline-none"
      />
      <input
        v-model="limit"
        type="number"
        min="1"
        max="50"
        title="Limit"
        class="outline-none border-l border-gray-300 px-2 py-2 w-14 text-center"
      />
      <!-- search-button-->
      <button
        type="submit"
        class="border rounded-md px-4 py-2 bg-blue-400 text-white font-semibold"
      >
        Search
      </button>
    </form>
    <!-- gifs-images-->
    <div class="flex flex-wrap gap-6 items-center justify-center px-50">
      <div v-for="gif in gifs" :key="gif.id">
        <!-- Image -->
        <img
          :src="gif.images.original.url"
          :alt="gif.title"
          class="w-40 h-40 object-cover rounded-lg"
        />

        <!-- GIF Title -->
        <h3 class="mt-2 font-bold text-gray-700 capitalize w-40 truncate">
          {{ gif.title || "Untitled GIF" }}
        </h3>

        <!-- Creator Name -->
        <p class="text-sm text-gray-500 italic">
          {{ gif.user?.display_name || gif.username || "Anonymous" }}
        </p>
      </div>
    </div>
  </div>
</template>
