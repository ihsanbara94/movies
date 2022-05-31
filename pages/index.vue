<template>
  <div>
    <Navigation />

    <div class="flex my-0 mx-auto px-0 py-3 max-w-[1140px] justify-center">
      <div class="px-4 block space-y-3 justify-start w-1/4">
        <p>Search</p>
        <input
          class="px-2 py-2 border-solid border-2 w-full"
          type="text"
          placeholder="Example: Iron man"
          @keyup.enter="getResult()"
          v-model="searchKey"
        />
        <button
          class="bg-blue-500 text-white px-4 py-2 rounded font-bold"
          @click="getResult()"
        >
          Search
        </button>

        <p>Filter Film</p>
        <div
          class="w-full block space-y-2 bg-white border-2 border-solid px-2 py-2"
        >
          <p>Year</p>
          <input
            type="text"
            placeholder="Input Year"
            class="px-2 py-2 border-solid border-2 w-full"
          />

          <p>Genre</p>
          <Dropdown
            class="border-2 border-solid w-full"
            @click="dropdownFilter"
            :listDropdown="listDropdownGenre"
            :choosedList="filter"
            :isOpen="isOpenFilter"
            @click-list="chooseFilter"
          />

          <p>Sort By</p>
          <Dropdown
            class="border-2 border-solid w-full"
            @click="dropdownFilterSort"
            :listDropdown="listDropdownSort"
            :choosedList="filterSort"
            :isOpen="isOpenFilterSort"
            @click-list="chooseFilterSort"
          />
        </div>
      </div>
      <div class="w-2/3">
        <div>
          <ContentCard :dataMovies="Movies" />
        </div>

        <button
          class="block justify-center mx-auto my-5 bg-blue-500 text-white px-4 py-2 rounded font-bold"
          @click="setPage()"
        >
          More
        </button>
      </div>
    </div>
  </div>
</template>

<script>
import axios from "axios";
import Navigation from "../components/Navigation.vue";
import ContentCard from "../components/ContentCard.vue";
import Dropdown from "../components/Dropdown.vue";
export default {
  components: { Navigation, ContentCard, Dropdown },
  data() {
    return {
      apiKey: "6de3c0f0176c22fabe34c6be66fa8cae",
      Movies: [],
      Search: [],
      page: 1,
      Year: [],
      Genre: [],
      Sort: [],
      filter: "E.g. Action ",
      isOpenFilter: false,
      listDropdownGenre: ["Ascending", "Descending"],
      filterSort: "E.g. Popularity ",
      isOpenFilterSort: false,
      listDropdownSort: ["ihsan", "BB"],
      searchKey: "",
      useFilter: false,
    };
  },
  watch: {
    page() {
      this.getDataMovies();
      this.getSearchMovie();
    },
  },
  mounted() {
    this.getDataMovies();
  },
  methods: {
    getDataMovies() {
      axios
        .get(
          `https://api.themoviedb.org/3/movie/now_playing?api_key=${this.apiKey}&page=${this.page}`
        )
        .then((res) => {
          this.Movies = [...this.Movies, ...res.data.results];
          // console.log(res.data.results);
        })
        .catch((err) => {
          console.log(err);
        });
    },

    getSearchMovie() {
      axios
        .get(
          `https://api.themoviedb.org/3/search/multi?api_key=${this.apiKey}&query=${this.searchKey}&page=${this.page}`
        )
        .then((res) => {
          this.Search = res.data.results;
          console.log(res.data.results);
        })
        .catch((err) => {
          console.log(err);
        });
    },
    setPage() {
      this.page += 1;
    },
    chooseFilter(param) {
      console.log(param);
      if (param === "Ascending") {
        this.search = this.search.sort(
          (a, b) =>
            parseInt(a.createdAt.split("-").join("")) -
            parseInt(b.createdAt.split("-").join(""))
        );
      } else if (param === "Descending") {
        this.search = this.search.sort(
          (a, b) =>
            parseInt(b.createdAt.split("-").join("")) -
            parseInt(a.createdAt.split("-").join(""))
        );
      } else {
        console.log(param);
      }
    },
    dropdownFilter() {
      this.isOpenFilter = !this.isOpenFilter;
    },
    dropdownFilterSort() {
      this.isOpenFilterSort = !this.isOpenFilterSort;
    },
    chooseFilterSort() {
      alert("ihsan");
    },
    getResult() {
      // state.page = 1
      // dataMovies.value = []
      // getDataMovies()
      this.useFilter = false;
      this.page = 1;
      this.Movies = [];
      this.getSearchMovie();
    },
  },
};
</script>
