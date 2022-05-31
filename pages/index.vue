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
            v-model="year"
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
          <button
            class="mx-auto my-5 bg-blue-500 text-white px-4 py-2 rounded font-bold"
            @click="executeFilter()"
          >
            Filter
          </button>
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
      listDropdownGenre: [
        "Action",
        "Animation",
        "Comedy",
        "Documentary",
        "Horror",
        "Romance",
      ],
      filterSort: "E.g. Popularity ",
      isOpenFilterSort: false,
      listDropdownSort: ["Popularity", "Release Date", "Revenue", "Most Vote"],
      searchKey: "",
      useFilter: false,
      urlDiscover: "https://api.themoviedb.org/3/discover/movie",
      urlSearch: "https://api.themoviedb.org/3/search/movie",
      sortBy: "popularity.desc",
      year: "",
      sortBy: "",
      filterGenreById: 0,
    };
  },
  watch: {
    page() {
      this.getDataMovies();
    },
  },
  mounted() {
    this.getDataMovies();
  },
  methods: {
    getDataMovies() {
      axios
        .get(
          this.setEndpointParam(this.sortBy, this.year, this.filterGenreById)
        )
        .then((res) => {
          this.Movies = res.data.results;
        })
        .catch((err) => {
          console.log(err);
        });
    },

    setEndpointParam(sortBy, year, filterGenreById) {
      if (!this.useFilter) {
        let url =
          this.searchKey == ""
            ? `${this.urlDiscover}?api_key=${this.apiKey}&page=${this.page}`
            : `${this.urlSearch}?api_key=${this.apiKey}&query=${this.searchKey}&page=${this.page}`;
        return url;
      } else {
        let url = `${this.urlDiscover}?api_key=${this.apiKey}&language=en-US&page=${this.page}`;
        year && (url += `&primary_release_year=${year}`);
        filterGenreById && (url += `&with_genres=${filterGenreById}`);
        sortBy && (url += `&sort_by=${sortBy}`);
        return url;
      }
    },

    executeFilter() {
      this.useFilter = true;
      if (this.year || this.filterGenreById || this.sortBy) {
        this.Movies.value = [];
        this.getDataMovies();
      }
    },

    chooseFilter(param) {
      console.log(param);
      if (param === "Action") {
        this.filterGenreById = 28;
        this.filter = "Action";
        this.isOpenFilter = !this.isOpenFilter;
      } else if (param === "Animation") {
        this.filterGenreById = 16;
        this.filter = "Animation";
        this.isOpenFilter = !this.isOpenFilter;
      } else if (param === "Comedy") {
        this.filterGenreById = 35;
        this.filter = "Comedy";
        this.isOpenFilter = !this.isOpenFilter;
      } else if (param === "Documentary") {
        this.filterGenreById = 99;
        this.filter = "Documentary";
        this.isOpenFilter = !this.isOpenFilter;
      } else if (param === "Horror") {
        this.filterGenreById = 27;
        this.filter = "Horror";
        this.isOpenFilter = !this.isOpenFilter;
      } else if (param === "Romance") {
        this.filterGenreById = 10749;
        this.filter = "Romance";
        this.isOpenFilter = !this.isOpenFilter;
      } else {
        console.log(param);
      }
    },
    chooseFilterSort(param) {
      console.log(param);
      if (param === "Popularity") {
        this.sortBy = "popularity.desc";
        this.filterSort = "Popularity";
        this.isOpenFilterSort = !this.isOpenFilterSort;
      } else if (param === "Release Date") {
        this.sortBy = "release_date.desc";
        this.filterSort = "Release Date";
        this.isOpenFilterSort = !this.isOpenFilterSort;
      } else if (param === "Revenue") {
        this.sortBy = "revenue.desc";
        this.filterSort = "Revenue";
        this.isOpenFilterSort = !this.isOpenFilterSort;
      } else if (param === "Most Vote") {
        this.sortBy = "vote_count.desc";
        this.filterSort = "Most Vote";
        this.isOpenFilterSort = !this.isOpenFilterSort;
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
    chooseFilterSortSort() {
      alert("ihsan");
    },
    setPage() {
      this.page += 1;
    },
    getResult() {
      this.useFilter = false;
      // this.page = 1;
      this.Movies = [];
      this.getDataMovies();
    },
  },
};
</script>
