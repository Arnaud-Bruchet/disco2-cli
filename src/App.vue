<template>
  <div id="app">
    <nav class="navbar navbar-expand-md navbar-dark fixed-top bg-dark">
      <a class="navbar-brand" href="#">App Tilte</a>
      <button class="navbar-toggler" type="button" data-toggle="collapse" data-target="#navbarCollapse"
        aria-controls="navbarCollapse" aria-expanded="false" aria-label="Toggle navigation">
        <span class="navbar-toggler-icon"></span>
      </button>
      <div class="collapse navbar-collapse" id="navbarCollapse">
        <ul class="navbar-nav mr-auto">
          <li class="nav-item active">
            <a class="nav-link" href="#">Home <span class="sr-only">(current)</span></a>
          </li>
          <li class="nav-item">
            <a class="nav-link" href="#">Link</a>
          </li>
        </ul>
      </div>
    </nav>
    <main role="main" class="fluid-container m-4">
      <div class="row">
        <div class="col-8">
          <div class="card mb-3" v-for="(record, index) in filterResults" :key="index"
            :class="record.pitchforkPos<=10 ? 'topAlbum' : ''" @click="favorite(index)">
            <div class="row no-gutters">
              <div class="col-m-4 m-2">
                <img height="150px"
                  :src="record.hasOwnProperty('coverUrl') ? record.coverUrl : 'https://www.terreseteaux.fr/images/resized/error.png?format=serviceThumbnail&width=280&height=280&size=1&cover=true&extension=jpeg'"
                  alt="..." />
              </div>
              <div class="col-md-8">
                <div class="card-body">
                  <h5 class="card-title">#{{ record.pitchforkPos }} {{ record.artist }}
                    <span class="card-text" v-if="record.isFavorite === true">❤</span>
                  </h5>
                  <p class="card-text">{{ record.title }} ({{ record.year }})</p>
                </div>
              </div>
            </div>

            <div class="card-footer" :class="record.stock < 1 ? 'negatif' : ''">
              <button type="button" class="btn btn-info" @click.stop="incrementStock(index)">[+]</button>
              <button type="button" class="btn btn-info mx-4" @click.stop="decrementStock(index)">[-]</button>
              <small class=""> {{ record.stock }} en Stock </small>
            </div>
          </div>
        </div>
        <div class="col-4">
          <div class="card bg-light mb-3" style="max-width: 18rem;">
            <div class="card-header">Infos albums (<strong>{{ filterResults.length}}</strong>)</div>
            <div class="card-body">
              <h5 class="card-title">Option selec:</h5>
              <select class="custom-select" v-model="sortOption" @change="sortOptions">
                <option value="none">None</option>
                <option value="posAsc">Pitchfork Pos ⬆</option>
                <option value="posDesc">Pitchfork Pos ⬇</option>
                <option value="byYear">Année de sortie</option>
              </select>
              <p class="card-text">Some sorting options</p>
              <h5 class="card-title">Filter</h5>
              <div class="custom-control custom-switch">
                <input type="checkbox" class="custom-control-input" id="customSwitch1" v-model="onlyInStock"
                  @change="onlyAvailableFilter()" />
                <label class=" custom-control-label" for="customSwitch1">Que ceux disponible</label>
              </div>
              <div></div>
            </div>
          </div>
        </div>
      </div>
    </main>
  </div>
</template>

<script>
import { records } from "./assets/static/js/allRecords";

export default {
  name: "App",
  components: {},
  data: () => ({
    records,
    filterResults: records,
    onlyInStock: false,
    sortOption: '',
  }),
  methods: {
    hasCover(albumId) {
      return this.records[albumId]?.coverUrl ? this.records[albumId].coverUrl : this.cover
    },
    decrementStock(albumId) {
      this.records[albumId].stock--;
    },
    incrementStock(albumId) {
      this.records[albumId].stock++;
    },
    onlyAvailableFilter() {
      console.log('changement filtre ', this.onlyInStock)

      let results = (this.onlyInStock) ? this.records.filter(record => record.stock > 0) : this.records.filter(record => record.stock >= 0)

      this.filterResults = results
      console.log(results)

    },
    sortOptions() {
      if (this.sortOption === 'byYear'){
        this.filterResults.sort((a, b) => parseInt(a.year) - parseInt(b.year))
      }
      else if (this.sortOption === 'posAsc'){
        this.filterResults.sort((a, b) => a.pitchforkPos - b.pitchforkPos)
      }
      else if (this.sortOption === 'posDesc'){
        this.filterResults.sort((a, b) => b.pitchforkPos - a.pitchforkPos)
      }
      else if (this.sortOption === 'none'){
        this.onlyAvailableFilter()
      }
    },
    favorite(index) {
      if (this.filterResults[index].isFavorite){
        this.filterResults[index].isFavorite = false
        // this.filterResults[index].isFavorite = false
      }
      else {
        this.$set(this.filterResults[index], 'isFavorite', true)
        // this.filterResults[index].isFavorite = true
      }
      // console.log(this.filterResults[index])
    }
  },
  created() {
    console.log("hey Created");
  },
};
</script>
<style lang="scss">
@import "./assets/navbar-top-fixed.css";
@import "../node_modules/bootstrap/scss/bootstrap.scss";

.negatif {
  background-color: rgb(241, 22, 22);
}
.topAlbum {
  background-color: rgb(236, 248, 14);
}
</style>
