<template>
  <div class="container">
    <h1>Datatable with Vuetify + Vue JS With Spring Boot REST API</h1>
    <v-data-table
      :page="page"
      :pageCount="numberOfPages"
      :headers="headers"
      :items="books"
      :options.sync="options"
      :server-items-length="totalBooks"
      :loading="loading"
      class="elevation-1"
      :footer-props="{
    'items-per-page-options': [5, 10, 20, 30, 40, 50]
  }"
    >

    <template v-slot:item.author="{ item }">
        {{item.author.firstName}} {{item.author.lastName}}
      </template>

    <template v-slot:item.imageUrl="{ item }">
        <img :src="item.imageUrl" style="width: 10%;" />
      </template>
    </v-data-table>
  </div>
</template>
<script>
import axios from "axios";
export default {
  name: "Dashboard",
  data() {
    return {
      page: 1,
      totalBooks: 0,
      numberOfPages: 0,
      books: [],
      loading: true,
      options: {},
      headers: [
        { text: "Name", value: "name" },
        { text: "ISBN", value: "isbn" },
        { text: "Author", value: "author" },
        { text: "Image", value: "imageUrl" }
      ],
    };
  },
  //this one will populate new data set when user changes current page. 
  watch: {
    options: {
      handler() {
        this.readDataFromAPI();
      },
    },
    deep: true,
  },
  methods: {
    //Reading data from API method. 
    readDataFromAPI() {
      this.loading = true;
      const { page, itemsPerPage } = this.options;
      let pageNumber = page - 1;
      console.log("http://localhost:8081/api/library/book/search?size=" +
            itemsPerPage +
            "&page=" +
            pageNumber)
      axios
        .get(
          "http://localhost:8081/api/library/book/search?size=" +
            itemsPerPage +
            "&page=" +
            pageNumber
        )
        .then((response) => {
          //Then injecting the result to datatable parameters.
          this.loading = false;
          this.books = response.data.bookList;
          this.totalBooks = response.data.numberOfItems;
          this.numberOfPages = response.data.numberOfPages;
        });
    },
    getAuthor(value) {
      console.log("Getting author "+value);
      return value;
    }
  },
  //this will trigger in the onReady State
  mounted() {
    this.readDataFromAPI();
  },
};
</script>