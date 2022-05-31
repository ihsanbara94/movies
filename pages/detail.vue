<template>
  <div>
    <Navigation />
    <div class="detail">
      <div class="container">
        <div v-if="type === 'movie'">
          <div class="flex justify-center space-x-10">
            <img
              class="poster"
              :src="`http://image.tmdb.org/t/p/w500/${dataDetail.poster_path}`"
            />
            <div class="col-md-6">
              <h1 class="bold text-4xl my-5">{{ dataDetail.title }}</h1>
              <div class="info">
                Release Date:
                <span class="bold">{{ dataDetail.release_date }}</span
                ><br />
                Genre:
                <span class="bold">{{ setNames(dataDetail.genres) }}-</span>
                <br />
              </div>
              <br />
              <div class="overview">
                <p>{{ dataDetail.overview }}</p>
              </div>
            </div>
          </div>
        </div>
      </div>
    </div>
  </div>
</template>

<script>
import axios from "axios";
import Navigation from "../components/Navigation.vue";
export default {
  components: { Navigation },
  data() {
    return {
      type: "",
      typeId: "",
      apiKey: "6de3c0f0176c22fabe34c6be66fa8cae",
      dataDetail: [],
    };
  },
  beforeMount() {
    this.type = this.$route.query.type;
    this.typeId = this.$route.query.typeId;
    this.getDataDetail();
  },
  methods: {
    setNames(arrayNames) {
      let names = "";
      arrayNames &&
        arrayNames.forEach((element) => {
          names += ` ${element.name},`;
        });
      return names;
    },
    setTime(arrayNames) {
      let names = "";
      arrayNames &&
        arrayNames.forEach((element) => {
          names += ` ${element}m,`;
        });
      return names;
    },
    getDataDetail() {
      axios
        .get(
          `https://api.themoviedb.org/3/${this.type}/${this.typeId}?api_key=${this.apiKey}`
        )
        .then((res) => {
          this.dataDetail = res.data;
        })
        .catch((err) => {
          console.log(err);
        });
    },
  },
};
</script>
<style>
.detail {
  padding: 16px 0;
  margin: 0 auto;
  max-width: 740px;
}

.bold {
  font-weight: bold;
}

.poster {
  width: 20rem;
  border-radius: 10px;
}

@media only screen and (max-width: 576px) {
  .poster {
    width: 50%;
  }
}

@media only screen and (max-width: 768px) {
  .container {
    font-size: 12px;
  }
}
</style>
