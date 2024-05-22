<template>
  <div class="container">
    <!-- Header dengan menu -->
    <header class="header">
      <div class="logo-container">
        <img
          src="c:\Users\HP\Downloads\logo TodoPost.png"
          alt="Logo"
          class="logo"
        />
        <h1 class="app-name">TodoPost</h1>
      </div>
      <nav class="menu">
        <ul>
          <li @click="pilihMenu('todos')" :class="{ 'aktif': menuAktif === 'todos' }">Todos</li>
          <li @click="pilihMenu('post')" :class="{ 'aktif': menuAktif === 'post' }">Post</li>
          <li @click="pilihMenu('kalkulator')" :class="{ 'aktif': menuAktif === 'kalkulator' }">Kalkulator</li>

        </ul>
      </nav>
    </header>
    
    <!-- Konten yang bergantung pada pilihan menu -->
    <div class="konten">
      <div v-if="menuAktif === 'todos'">
        <!-- Komponen TodoList -->
        <TodoList />
      </div>
      <div v-else-if="menuAktif === 'post'">
        <!-- Komponen Post -->
        <Post />
      </div>
      <div v-else-if="menuAktif === 'kalkulator'">
        <!-- Komponen Post -->
        <Kalkulator />
      </div>
    </div>
  </div>
</template>

<script>
// Impor komponen TodoList dan Post
import TodoList from './components/TodoList.vue';
import Post from './components/Post.vue';
import Kalkulator from './components/Kalkulator.vue';

export default {
  components: {
    TodoList,
    Post,
    Kalkulator

  },
  data() {
    return {
      menuAktif: 'todos' // Menu aktif awal
    };
  },
  methods: {
    pilihMenu(menu) {
      this.menuAktif = menu;
    }
  }
};
</script>

<style scoped>
/* Gaya yang sudah ada */

/* Gaya untuk container */
.container {
  padding-top: 20px; /* Jarak antara header dengan konten */
}

/* Gaya untuk header */
.header {
  display: flex;
  justify-content: space-between;
  align-items: center;
  padding: 20px;
  padding-bottom: 10px; /* Tambahkan padding di bagian bawah header */
  background-color: #f9c8c8;
  color: #130101;
  border-radius: 10px;
  border-bottom: 2px solid #f44d4d; /* Garis bawah pada header */
}

.logo-container {
  display: flex;
  align-items: center;
}

.logo {
  width: 50px;
  height: 50px;
  margin-right: 10px;
}

.app-name {
  font-size: 24px;
  margin: 0;
}

.menu ul {
  list-style-type: none;
  margin: 0;
  padding: 0;
  display: flex;
}

.menu ul li {
  margin-right: 20px;
  cursor: pointer;
}

.menu ul li.aktif {
  font-weight: bold;
}

.post {
  padding-top: 20px;
}
</style>
