<script>
import axios from "axios";
export default {
  name: "App",
  data() {
    return {
      base_api_url: "http://127.0.0.1:8000",
      photo_endpoint: "/api/photos",
      photos: "",
      search_photo: "",
      categories: [],
      selected_category: "",
    };
  },
  methods: {
    search() {
      const paramsObj = {};

      if (this.selected_category) {
        paramsObj.category = this.selected_category;
      }
      if (this.search_photo) {
        paramsObj.search = this.search_photo;
      }

      console.log("ParamsObj:", paramsObj);
      const searchParams = new URLSearchParams(paramsObj);
      console.log("SearchParams:", searchParams);
      let url =
        this.base_api_url + this.photo_endpoint + `?${searchParams.toString()}`;

      console.log("URL generato:", url);
      this.callApi(url);
    },

    callApi(url) {
      axios
        .get(url)
        .then((resp) => {
          console.log(resp);
          this.photos = resp.data.results;
        })
        .catch((err) => {
          console.error(err);
        });
    },

    fetchCategories() {
      axios
        .get(this.base_api_url + "/api/categories")
        .then((resp) => {
          this.categories = resp.data.results;
          console.log(resp.data.results);
        })
        .catch((err) => {
          console.error(err);
        });
    },
  },
  mounted() {
    const url = this.base_api_url + this.photo_endpoint;
    //console.log(url);
    this.callApi(url);
    this.fetchCategories();
  },
};
</script>
<template>
  <div class="p-5 mb-4 bg-light rounded-3">
    <div class="container py-5">
      <h1 class="display-5 fw-bold">Photo</h1>
      <p class="col-md-8 fs-4">Take a look at my amazing photos</p>
    </div>
  </div>
  <div class="container">
    <div class="row">
      <div>
        <form class="d-flex" @submit.prevent="search()">
          <div class="input-group input-group-lg mb-3">
            <input
              type="search"
              class="form-control"
              placeholder="search..."
              v-model="search_photo"
            />

            <button class="btn btn-outline-secondary" type="search">
              <i class="fa fa-search fa-lg fa-fw"></i>
            </button>
          </div>
          <select
            class="form-select"
            aria-label="Default select example"
            v-model="selected_category"
            @change="search()"
          >
            <option selected>Select Category</option>
            <option
              v-for="category in categories"
              :key="category.id"
              :value="category.id"
            >
              {{ category.name }}
            </option>
          </select>
        </form>
      </div>
    </div>
  </div>

  <section class="photos" v-if="photos">
    <div class="container">
      <div class="row row-cols-1 row-cols-sm-2 row-cols-md-3">
        <div class="col" v-for="photo in photos.data">
          <div class="card mx-2 my-2">
            <div v-if="photo.image_path">
              <img
                class="card-img-top"
                :src="
                  photo.image_path.startsWith('https://')
                    ? 'https://picsum.photos/400/200'
                    : base_api_url + '/storage' + photo.image_path
                "
                alt=""
              />
            </div>
            <div class="card-body">
              {{ photo.title }}
            </div>
          </div>
        </div>
      </div>
      <nav aria-label="Page navigation" class="mt-4">
        <ul class="pagination">
          <li class="page-item disabled" v-for="link in photos.links">
            <a class="page-link" href="#" aria-label="Previous"> &laquo </a>
          </li>
          <li class="page-item active" aria-current="page">
            <a class="page-link" href="#">1</a>
          </li>
          <li class="page-item"><a class="page-link" href="#">2</a></li>
          <li class="page-item">
            <a class="page-link" href="#">3</a>
          </li>
          <li class="page-item">
            <a class="page-link" href="#" aria-label="Next"> &raquo </a>
          </li>
        </ul>
      </nav>
    </div>
  </section>
</template>

<style></style>
