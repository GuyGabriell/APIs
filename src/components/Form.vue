<script setup>
import { ref } from "vue";

const query = ref("");
const limit = ref("");
const gifs = ref([]);
const isLoading = ref(false);

//Error states
const queryError = ref("");
const limitError = ref("");

//this validates the query functions
const validateQuery = () => {
  if (!query.value) {
    queryError.value = "Please provide a search term here.";
  } else {
    queryError.value = "";
  }
};

//this validates the limit functions
const validateLimit = () => {
  if (!limit.value) {
    limitError.value = "Please provide a limit value here.";
  } else {
    limitError.value = "";
  }
};

const fetchGifs = async () => {
  //Final check before fetching the content
  validateQuery();
  validateLimit();

  if (queryError.value || limitError.value) return;

  isLoading.value = true;

  try {
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
  } catch (error) {
    console.error("Error fetching gifs:", error);
  } finally {
    isLoading.value = false;
    query.value = "";
    limit.value = "";
  }
};
</script>

<template>
  <!-- change from h-screen to min-h-screen to fit both for mobile -->
  <div class="flex flex-col items-center min-h-screen px-6">
    <!-- header -->
    <h1
      class="text-2xl md:text-3xl font-bold text-blue-300 text-center mt-10 mb-5"
    >
      GIPHY MAKES SEARCH EASY...
    </h1>

    <!-- form -->
    <form
      @submit.prevent="fetchGifs"
      class="flex flex-col md:flex-row items-start gap-2 mb-10 w-full md:w-auto"
    >
      <!-- query-input -->
      <div class="flex flex-col w-full md:w-64">
        <input
          v-model="query"
          @focus="validateQuery"
          @input="validateQuery"
          type="text"
          placeholder="Search for a GIF..."
          class="border border-gray-300 rounded-md px-4 py-2 w-full outline-none focus:border-blue-400"
        />
        <!--error message-->
        <p v-if="queryError" class="text-red-500 text-xs mt-1">
          {{ queryError }}
        </p>
      </div>

      <!-- limit-input -->
      <div class="flex flex-col w-full md:w-64">
        <input
          v-model="limit"
          @focus="validateLimit"
          @input="validateLimit"
          type="number"
          min="1"
          max="50"
          placeholder="Search from (1-50)"
          class="border border-gray-300 rounded-md px-4 py-2 w-full outline-none focus:border-blue-400"
        />
        <!--error message-->
        <p v-if="limitError" class="text-red-500 text-xs mt-1">
          {{ limitError }}
        </p>
      </div>
      <!-- search button -->
      <button
        type="submit"
        :disabled="isLoading"
        class="w-full md:w-auto rounded-md px-6 py-2 bg-blue-400 text-white font-semibold disabled:bg-blue-200"
      >
        {{ isLoading ? "Searching..." : "Search" }}
      </button>
    </form>

    <!-- spinner -->
    <div v-if="isLoading" class="flex justify-center items-center py-20">
      <div
        class="animate-spin rounded-full h-12 w-12 border-t-2 border-b-2 border-blue-400"
      ></div>
    </div>

    <!-- gifs-images -->
    <div
      v-else
      class="flex flex-wrap gap-6 items-center justify-center md:px-46 pb-10"
    >
      <div
        v-for="gif in gifs"
        :key="gif.id"
        class="flex flex-col w-full md:w-40"
      >
        <img
          :src="gif.images.original.url"
          :alt="gif.title"
          class="w-full h-46 md:w-40 md:h-40 object-cover rounded-lg"
        />
        <!-- gif-title -->
        <h3 class="mt-2 font-bold text-gray-700 capitalize w-40 truncate">
          {{ gif.title || "Untitled GIF" }}
        </h3>
        <!-- gif-user/creator -->
        <p class="text-sm text-gray-500 italic">
          {{ gif.user?.display_name || gif.username || "Anonymous" }}
        </p>
      </div>
    </div>
  </div>
</template>
