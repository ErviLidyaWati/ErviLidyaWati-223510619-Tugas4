<template>
  <div class="post">
    <h2 class="post-title">Postingan</h2>
    <div class="select-container">
      <label for="user-select" class="select-label">Pilih Pengguna:</label>
      <select v-model="selectedUsers" id="user-select" @change="loadPosts" class="user-select" multiple>
        <option v-for="user in users" :key="user.id" :value="user.id">{{ user.name }}</option>
      </select>
    </div>
    <div v-if="selectedUsers.length > 0">
      <div v-for="userId in selectedUsers" :key="userId">
        <template v-if="postsByUser[userId] && postsByUser[userId].length > 0">
          <h3 class="post-item-title">{{ users.find(u => u.id === userId).name }}</h3>
          <div class="post-list">
            <div class="post-item" v-for="(post, index) in postsByUser[userId]" :key="post.id" :style="{ backgroundColor: post.backgroundColor }">
              <h3 class="post-item-title" :style="{ color: getTextColor(post.backgroundColor) }">{{ post.title }} - Postingan {{ getPostNumber(post.id) }} dari {{ totalPosts }}</h3>
              <p class="post-item-body" :style="{ color: getTextColor(post.backgroundColor) }">{{ post.body }}</p>
              <div v-if="index === 0" class="post-user-info">
                <span class="user-info-label">Pengguna:</span>
                <span class="user-info">{{ users.find(u => u.id === userId).name }}</span>
                <span class="user-info-label">Email:</span>
                <span class="user-info">{{ users.find(u => u.id === userId).email }}</span>
                <span class="user-info-label">Alamat:</span>
                <span class="user-info">{{ users.find(u => u.id === userId).address.city }}, {{ users.find(u => u.id === userId).address.street }}, {{ users.find(u => u.id === userId).address.suite }}, {{ users.find(u => u.id === userId).address.zipcode }}</span>
              </div>
            </div>
          </div>
        </template>
      </div>
    </div>
    <div v-else>
      <p class="no-post-message">Pilih satu atau lebih pengguna untuk melihat postingan mereka.</p>
    </div>
  </div>
</template>

<script>
export default {
  data() {
    return {
      selectedUsers: [],
      users: [],
      postsByUser: {}, // Data untuk menyimpan postingan berdasarkan pengguna
      totalPosts: 0 // Total postingan dari semua pengguna
    };
  },
  methods: {
    async loadPosts() {
      try {
        const requests = this.selectedUsers.map(async userId => {
          const response = await fetch(`https://jsonplaceholder.typicode.com/posts?userId=${userId}`);
          const posts = await response.json();
          return {
            userId: userId,
            posts: posts.map((post, index) => ({
              ...post,
              backgroundColor: this.getRandomColor(),
              postId: post.id // Menambahkan ID postingan untuk identifikasi unik
            }))
          };
        });

        const results = await Promise.all(requests);
        this.postsByUser = results.reduce((acc, { userId, posts }) => {
          acc[userId] = posts;
          return acc;
        }, {});

        // Menghitung total postingan dari semua pengguna
        this.totalPosts = results.reduce((total, { posts }) => total + posts.length, 0);
      } catch (error) {
        console.error('Error fetching posts:', error);
      }
    },
    getRandomColor() {
      const letters = '0123456789ABCDEF';
      let color = '#';
      for (let i = 0; i < 6; i++) {
        color += letters[Math.floor(Math.random() * 16)];
      }
      return color;
    },
    async loadUsers() {
      try {
        const response = await fetch('https://jsonplaceholder.typicode.com/users');
        this.users = await response.json();
      } catch (error) {
        console.error('Error fetching users:', error);
      }
    },
    // Mendapatkan nomor postingan berdasarkan ID postingan
    getPostNumber(postId) {
      let postNumber = 0;
      for (const key in this.postsByUser) {
        const posts = this.postsByUser[key];
        const foundPost = posts.find(post => post.postId === postId);
        if (foundPost) {
          postNumber += posts.indexOf(foundPost) + 1;
          break;
        }
        postNumber += posts.length;
      }
      return postNumber;
    },
    // Mendapatkan warna teks yang kontras berdasarkan warna latar belakang
    getTextColor(backgroundColor) {
      // Konversi warna latar belakang ke RGB
      const rgb = parseInt(backgroundColor.substring(1), 16);
      const r = (rgb >> 16) & 0xff;
      const g = (rgb >> 8) & 0xff;
      const b = (rgb >> 0) & 0xff;

      // Hitung kecerahan
      const brightness = (r * 299 + g * 587 + b * 114) / 1000;

      // Jika kecerahan cukup tinggi, gunakan warna teks gelap
      return brightness > 125 ? '#000000' : '#ffffff';
    }
  },
  mounted() {
    this.loadUsers();
  }
};
</script>

<style scoped>
/* Gaya untuk Post */
.post {
  max-width: 800px;
  margin: 0 auto;
  padding: 20px;
  border-radius: 10px;
  background-color: pink;
  box-shadow: 0 4px 8px rgba(0, 0, 0, 0.1);
}

.post-title {
  text-align: center;
  margin-bottom: 20px;
  font-size: 24px;
  color: #333;
}

.select-container {
  margin-bottom: 20px;
}

.select-label {
  display: block;
  font-weight: bold;
  margin-bottom: 5px;
  font-size: 16px;
  color: #333;
}

.user-select {
  width: 100%;
  padding: 10px;
  font-size: 16px;
  border-radius: 5px;
}

.post-list {
  border-radius: 10px;
}

.post-item {
  border: 1px solid #ccc;
  margin-bottom: 20px;
  padding: 20px;
  border-radius: 10px;
}

.post-item-title {
  font-size: 20px;
  margin-top: 0;
  margin-bottom: 10px;
}

.post-item-body {
  font-size: 16px;
  margin-top: 0;
  margin-bottom: 10px;
}

.post-user-info {
  margin-top: 10px;
}

.user-info-label {
  font-weight: bold;
}

.user-info {
  margin-left: 5px;
}

.no-post-message {
  text-align: center;
  font-style: italic;
  color: #080000;
  font-size: 16px;
}
</style>
