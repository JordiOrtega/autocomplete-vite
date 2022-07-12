<template>
  <div class="bg-gray-50 min-w-screen min-h-screen flex">
    <div class="max-w-xs relative space-y-3">
      <label for="search" class="text-gray-900">
        Type the name of a town or a region
      </label>

      <input
        type="text"
        id="search"
        v-model="searchTerm"
        placeholder="Type here..."
        class="p-3 mb-0.5 w-full border border-gray-300 rounded"
      />

      <ul
        v-if="townsR.length || regionsR.length"
        class="
          mt-0
          w-full
          rounded
          bg-white
          border border-gray-300
          px-4
          py-2
          space-y-1
          absolute
          z-10
          text-left
        "
      >
        <!-- <li class="px-1 pt-1 pb-2 font-bold border-b border-gray-200">
          Showing results
        </li> -->
        <li class="px-1 pt-2 pb-2 text-slate-400 text-sm tracking-wider">
          Towns
        </li>
        <li
          v-for="town in townsR"
          :key="town.name"
          @click="selectCountry(town.name)"
          class="cursor-pointer hover:bg-gray-100 px-1"
          v-html="formatName(town.name)"
        />
        <li
          v-if="regionsR.length"
          class="px-1 pt-6 pb-2 text-slate-400 text-sm tracking-wider"
        >
          Regions
        </li>
        <li
          v-for="region in regionsR"
          :key="region.name"
          class="cursor-pointer hover:bg-gray-100 px-1"
          v-html="formatName(region.name)"
        />
      </ul>

      <p v-if="selectedCountry" class="text-lg pt-2 absolute">
        You have selected:
        <span class="font-semibold">{{ selectedCountry.name }}</span>
      </p>
    </div>
  </div>
</template>

<script>
import Spinner from './Spinner';
// inspiration from https://stevencotterill.com/articles/how-to-build-an-autocomplete-field-with-vue-3
export default {
  name: 'Autocomplete',
  components: { Spinner },
  data() {
    return {
      searchTerm: '',
      townsR: [],
      regionsR: [],
    };
  },
  watch: {
    searchTerm: {
      handler: 'fetchData',
      immediate: true,
    },
  },
  methods: {
    formatName(name) {
      const regex = new RegExp(this.searchTerm, 'ig');
      const splits = name.replace(
        regex,
        `<span class="text-lg font-extrabold">${this.searchTerm}</span>`
      );
      return splits;
    },
    async fetchData(newSearchTerm) {
      if (!newSearchTerm) {
        this.initResults();
      }

      const response = await fetch(
        `https://dc-test-react.vercel.app/api/cart?name=${newSearchTerm}`
      );
      const { regions, towns } = await response.json();
      this.townsR = towns
        .map((value) => {
          return { name: value.town };
        })
        .slice(0, 5);
      this.regionsR = regions
        .map((name) => {
          return { name };
        })
        .slice(0, 2);
    },
    initResults() {
      this.townsR = [];
      this.regionsR = [];
    },
  },
};
</script>
