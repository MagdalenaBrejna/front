<template>
  <CustomNavbar></CustomNavbar>
  <br><br>
  <div id="authors-table" class="container">
    <h4>Lista autorów książek</h4>
    <table>
      <thead>
      <tr>
        <th>Imię</th>
        <th>Nazwisko</th>
        <th>Usuń</th>
        <th>Aktualizuj</th>
      </tr>
      </thead>
      <tbody>
      <tr v-for="(author, index) in paginatedAuthors()" :key="author.id">
        <td>{{ author.firstName }}</td>
        <td>{{ author.lastName }}</td>
        <td>
          <button class="btn btn-danger" @click="deleteAuthor(author.id)" style="background-color:#d37676; border-color:#d37676;">Usuń</button>
        </td>
        <td>
          <button class="btn btn-primary" @click="this.$router.push({ name: 'authorUpdate', params: { id: author.id } });" style="background-color:#a0a0a0; border-color:#a0a0a0;">Aktualizuj</button>
        </td>
      </tr>
      </tbody>
    </table>
    <br>

    <div class="pagination">
      <button class="btn btn-primary" :disabled="currentPage === 1" @click="currentPage--" style="background-color:#2aadcd; border-color:#16b9e1;">Poprzednia</button>
      <span class="ms-3 me-3">Strona {{ currentPage }} z {{ totalPages }}</span>
      <button class="btn btn-primary" :disabled="currentPage === totalPages" @click="currentPage++" style="background-color:#2aadcd; border-color:#16b9e1;">Następna</button>
    </div>
    <AuthorForm :authorsSource="authors" class="mt-5"/>
  </div>
</template>

<script>
import axios from "axios";
import AuthorForm from "@/components/author/AuthorForm.vue";
import CustomNavbar from "@/components/utils/CustomNavbar.vue";

export default {
  name: 'Autorzy',
  components: {CustomNavbar, AuthorForm},

  data() {
    return {
      authors: [],
      currentPage: 1,
      itemsPerPage: 3,
    }
  },

  mounted() {
    this.getAuthors()
  },
  methods: {
    paginatedAuthors() {
      const startIndex = (this.currentPage - 1) * this.itemsPerPage;
      const endIndex = startIndex + this.itemsPerPage;
      return this.authors.slice(startIndex, endIndex);
    },
    async getAuthors() {
      try {
        const response = await fetch('http://localhost:8081/authors')
        const data = await response.json()
        this.authors = data
      } catch (error) {
        console.error(error)
      }
    },
    async deleteAuthor(itemId) {
      axios.delete(`http://localhost:8081/authors/${itemId}`)
          .then(response => {
            // Obsługa odpowiedzi serwera
            console.log(response.data);
          })
          .catch(error => {
            // Obsługa błędów
            this.$router.push({ name: 'ErrorPageA', params: { id: error } });
            console.error(error);
          });
      this.authors = this.authors.filter(obj => {
        return obj.id !== itemId
      });
    }
  },
  computed: {
    totalPages() {
      return Math.ceil(this.authors.length / this.itemsPerPage);
    }
  },

}
</script>

<style scoped>
  body {
    background-color: lightgrey;
    color: black;
  }
  
  h4 {
    color: black;
    size: 20px;
  }

  .btn {
    background-color:#2aadcd;
    border-color:#16b9e1; 
    height: 30px; 
    width: 100px; 
    font-size: 16px !important;
    display: flex;
    justify-content: center;
    align-items: center;
  }

  .btn:hover {
    background-color:#47494a !important;
    border-color:#47494a !important; 
  }
</style>