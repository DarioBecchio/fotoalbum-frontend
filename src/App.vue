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
      selected_featured: "",
      featureds: [],
    };
  },
  methods: {
    search() {
      const paramsObj = {};
      if (this.selected_featured) {
        paramsObj.featured = this.selected_featured;
      }
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

    fetchFeatureds() {
      axios
        .get(this.base_api_url + "/api/featureds")
        .then((resp) => {
          this.featureds = resp.data.results;
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
    this.fetchFeatureds();
  },
};
</script>

<template>
  <header class="bg-dark py-3">
    <div class="container d-flex justify-content-between align-items-center">
      <div class="logo">
        <a href="#"
          ><img
            src="C:\MAMP\htdocs\Laravel\fotoalbum-frontend\public\pngtree-photography-logos-png-image_3586374.jpg"
            alt="Logo"
            class="img-fluid"
            height="50"
            width="50"
        /></a>
      </div>
      <nav class="navbar navbar-expand-lg navbar-dark">
        <button
          class="navbar-toggler"
          type="button"
          data-toggle="collapse"
          data-target="#navbarNav"
          aria-controls="navbarNav"
          aria-expanded="false"
          aria-label="Toggle navigation"
        >
          <span class="navbar-toggler-icon"></span>
        </button>
        <div class="collapse navbar-collapse" id="navbarNav">
          <ul class="navbar-nav ml-auto">
            <li class="nav-item"><a class="nav-link" href="#">Home</a></li>
            <li class="nav-item"><a class="nav-link" href="#">Portfolio</a></li>
            <li class="nav-item"><a class="nav-link" href="#">Service</a></li>
            <li class="nav-item"><a class="nav-link" href="#">Blog</a></li>
            <li class="nav-item"><a class="nav-link" href="#">Contact</a></li>
          </ul>
        </div>
      </nav>
      <div class="social-icons d-none d-lg-flex">
        <a href="#"><i class="fa-brands fa-instagram"></i></a>
        <a href="#"><i class="fa-brands fa-facebook"></i></a>
        <a href="#"><i class="fa-brands fa-x-twitter"></i></a>
      </div>
    </div>
  </header>
  <main>
    <section class="jumbotron text-center">
      <div class="container">
        <h1 class="jumbotron-heading">
          Benvenuti nel Mio Portfolio Fotografico
        </h1>
        <p class="lead text-muted">Scopri le mie ultime opere</p>
      </div>
    </section>
    <div class="container">
      <section class="searchbar mb-4">
        <form class="d-flex flex-wrap" @submit.prevent="search">
          <div class="input-group input-group-lg mb-3 flex-grow-1">
            <input
              type="search"
              class="form-control"
              placeholder="search..."
              v-model="search_photo"
            />
            <button class="btn btn-outline-secondary" type="submit">
              <i class="fa fa-search fa-lg fa-fw"></i>
            </button>
          </div>
          <select
            class="form-select ml-2 mb-3"
            aria-label="Default select example"
            v-model="selected_category"
            @change="search"
            name="Select a category"
          >
            <option value="" selected>Category</option>
            <option
              v-for="category in categories"
              :key="category.id"
              :value="category.id"
            >
              {{ category.name }}
            </option>
          </select>
          <select
            class="form-select ml-2 mb-3"
            aria-label="Default select example"
            v-model="selected_featured"
            @change="search"
            name="Select status"
          >
            <option value="" selected>Featured</option>
            <option
              v-for="featured in featureds"
              :key="featured.id"
              :value="featured.id"
            >
              {{ featured.option }}
            </option>
          </select>
        </form>
      </section>

      <section class="photo-gallery">
        <!-- Placeholder per le foto -->
        <div class="row">
          <div
            class="col-md-4 mb-4"
            v-for="photo in filteredPhotos"
            :key="photo.id"
          >
            <div class="card">
              <img :src="photo.url" class="card-img-top" :alt="photo.title" />
              <div class="card-body">
                <h5 class="card-title">{{ photo.title }}</h5>
                <p class="card-text">{{ photo.description }}</p>
              </div>
            </div>
          </div>
        </div>
      </section>
      <nav aria-label="Page navigation" class="mt-4">
        <ul class="pagination">
          <li
            class="page-item"
            :class="{ disabled: !link.url, active: link.active }"
            v-for="link in posts.links"
          >
            <button
              class="page-link"
              :href="link.url"
              type="button"
              @click="goTo(link.url)"
            >
              <span v-html="link.label"></span>
            </button>
          </li>
        </ul>
      </nav>
    </div>
  </main>
  <footer class="bg-dark text-white py-4">
    <div class="container">
      <div class="row">
        <div class="col-md-4">
          <h5>About Me</h5>
          <p>
            Fotografo appassionato con anni di esperienza nel catturare momenti
            preziosi. Contattami per servizi fotografici personalizzati.
          </p>
        </div>
        <div class="col-md-4">
          <h5>Quick Links</h5>
          <ul class="list-unstyled">
            <li><a href="#" class="text-white">Home</a></li>
            <li><a href="#" class="text-white">Portfolio</a></li>
            <li><a href="#" class="text-white">Servizi</a></li>
            <li><a href="#" class="text-white">Blog</a></li>
            <li><a href="#" class="text-white">Contatti</a></li>
          </ul>
        </div>
        <div class="col-md-4">
          <h5>Contact Info</h5>
          <ul class="list-unstyled">
            <li><i class="fa fa-envelope"></i> email@example.com</li>
            <li><i class="fa fa-phone"></i> +39 123 456 7890</li>
            <li>
              <i class="fa fa-map-marker"></i> Via Roma 123, 00100 Roma, Italia
            </li>
          </ul>
        </div>
      </div>
      <div class="text-center mt-3">
        <small>&copy; 2024 Tuo Nome. Tutti i diritti riservati.</small>
      </div>
    </div>
  </footer>
  <!--<div class="p-5 mb-4 bg-light rounded-3">
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
            name="Select a category"
          >
            <option value="" selected>Seleziona una categoria</option>
            <option
              v-for="category in categories"
              :key="category.id"
              :value="category.id"
            >
              {{ category.name }}
            </option>
          </select>
          <select
            class="form-select"
            aria-label="Default select example"
            v-model="selected_featured"
            @change="search()"
            name="Select status"
          >
            <option value="" selected>Seleziona uno status</option>
            <option
              v-for="featured in featureds"
              :key="featured.id"
              :value="featured.id"
            >
              {{ featured.option }}
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
  </section>-->
</template>

<style>
.img-fluid {
  height: 100%;
  border-radius: 50%;
}

body {
  font-family: Ubuntu, sans-serif;
}

.header .navbar-nav .nav-link {
  color: white !important;
  font-size: 18px;
  transition: color 0.3s ease;
}

.header .navbar-nav .nav-link:hover {
  color: #ddd !important;
}

.header .social-icons a {
  text-decoration: none;
  color: white;
  margin-left: 15px;
  padding: 5px;
  transition: color 0.3s ease, background-color 0.3s ease, opacity 0.3s ease;
}

.header .social-icons img:hover {
  opacity: 0.7;
}

.header .logo img {
  height: 50px;
}

main {
  background-color: #f0f0f0;
}

.jumbotron {
  background: url("C:\MAMP\htdocs\Laravel\fotoalbum-frontend\public\adam-bignell-e2Q0Hr5LgwU-unsplash.jpg")
    no-repeat center center;
  background-size: cover;
  color: white;
}

.jumbotron .container {
  background: rgba(0, 0, 0, 0.5);
  padding: 30px;
  border-radius: 10px;
}

.searchbar {
  background-color: #f8f9fa;
  padding: 20px;
  border-radius: 8px;
}

.searchbar .input-group {
  flex-grow: 1;
}

.searchbar .form-select {
  max-width: 200px;
  margin-left: 10px;
}

.photo-gallery .card {
  border: none;
  box-shadow: 0 4px 8px;
}
</style>
