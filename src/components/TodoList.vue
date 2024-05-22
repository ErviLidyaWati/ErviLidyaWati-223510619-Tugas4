<template>
  <div class="container">
    <div class="content">
      <div class="app">
        <header class="header">
          <div class="logo-container">
            <img
              src="c:\Users\HP\Downloads\Biru_Putih_Geometri_Sederhana_Y2K_Logo-removebg-preview.png"
              alt="Logo"
              class="logo"
            />
            <h1 class="app-name">TodoList</h1>
          </div>
          <div class="profile">
            <div class="profile-info">
              <p>Nama : <input type="text" v-model="tempUserName" @blur="updateUserName" /></p>
              <p>Email : <input type="text" v-model="tempUserEmail" @blur="updateUserEmail" /></p>
            </div>
          </div>
        </header>
        <h1>Daftar Kegiatan</h1>
        <div class="input-container">
          <input v-model="newActivity" placeholder="Tambahkan kegiatan baru" />
          <input type="date" v-model="newActivityDate" class="date-input" />
          <button @click="addActivity" class="add-btn">Tambah Kegiatan</button>
        </div>
        <div>
          <input type="checkbox" id="showCompleted" v-model="showCompleted" />
          <label for="showCompleted">Tampilkan Seluruh Daftar Kegiatan </label>
        </div>
        <table>
          <thead>
            <tr>
              <th>No</th>
              <th>Nama Kegiatan</th>
              <th>Tanggal</th>
              <th>Status</th>
              <th>Aksi</th>
            </tr>
          </thead>
          <tbody>
            <tr
              v-for="(activity, index) in filteredActivities"
              :key="index"
              :style="{ backgroundColor: getRowColor(activity.date) }"
            >
              <td>{{ index + 1 }}</td>
              <td :class="{ completed: activity.completed }">{{ activity.name }}</td>
              <td>{{ activity.date }}</td>
              <td>
                <label class="checkbox-label">
                  <input
                    type="checkbox"
                    :checked="activity.completed"
                    class="checkbox"
                    @change="toggleCompletion(activity)"
                  />
                  <span class="checkmark"></span>
                  <span v-if="activity.completed" class="status-label">Selesai</span>
                </label>
              </td>
              <td>
                <button class="delete-btn" @click="removeActivity(index)">Hapus</button>
              </td>
            </tr>
          </tbody>
        </table>
      </div>
    </div>
  </div>
</template>

<script>
export default {
  data() {
    return {
      activities: [],
      newActivity: '',
      newActivityDate: '',
      showCompleted: false,
      tempUserName: '',
      tempUserEmail: ''
    }
  },
  mounted() {
    this.loadData();
  },
  computed: {
    filteredActivities() {
      if (this.showCompleted) {
        return this.activities;
      } else {
        return this.activities.filter((activity) => !activity.completed);
      }
    }
  },
  watch: {
    activities: {
      handler() {
        this.saveData();
      },
      deep: true
    }
  },
  methods: {
    addActivity() {
      if (this.newActivity.trim() !== '' && this.newActivityDate !== '') {
        this.activities.push({
          name: this.newActivity,
          date: this.formatDate(this.newActivityDate),
          completed: false
        });
        this.newActivity = '';
        this.newActivityDate = '';
        this.saveData(); // Simpan data secara otomatis saat aktivitas baru ditambahkan
      }
    },
    removeActivity(index) {
      this.activities.splice(index, 1);
    },
    toggleCompletion(activity) {
      activity.completed = !activity.completed;
    },
    formatDate(date) {
      const d = new Date(date);
      const year = d.getFullYear();
      let month = '' + (d.getMonth() + 1);
      let day = '' + d.getDate();

      if (month.length < 2) {
        month = '0' + month;
      }
      if (day.length < 2) {
        day = '0' + day;
      }

      return [year, month, day].join('-');
    },
    getRowColor(date) {
      const d = new Date(date);
      const today = new Date();
      today.setHours(0, 0, 0, 0);

      if (d < today) {
        return '#f9c8c8'; // Merah muda untuk tanggal yang sudah lewat
      } else if (d > today) {
        return '#CCFFCC'; // Hijau muda untuk tanggal yang akan datang
      } else {
        return ''; // Kosongkan untuk tanggal hari ini
      }
    },
    saveData() {
      const key = `${this.tempUserEmail}_${this.tempUserName}`;
      localStorage.setItem(key, JSON.stringify(this.activities));
    },
    loadData() {
      const key = `${this.tempUserEmail}_${this.tempUserName}`;
      const data = localStorage.getItem(key);
      if (data) {
        this.activities = JSON.parse(data);
      }
    },
    updateUserName() {
      this.saveData();
    },
    updateUserEmail() {
      this.loadData();
    }
  }
}
</script>


<style scoped>
/* Gaya yang sudah ada */

.checkbox-label {
  position: relative;
  display: inline-block;
  padding-left: 35px; /* Lebar kotak ceklis + jarak */
  margin-right: 10px;
  cursor: pointer;
  user-select: none;
}

.checkbox-label .checkbox {
  display: none;
}

.checkbox-label .checkmark {
  position: absolute;
  top: 50%;
  left: 0;
  transform: translateY(-50%);
  height: 20px;
  width: 20px;
  background-color: #a4ebfb;
  border: 1px solid #fe8f8f;
  border-radius: 3px;
}

.checkbox-label .checkbox:checked + .checkmark {
  background-color: #28a745; /* Hijau untuk checkbox yang dicentang */
  border: 1px solid #28a745;
}

.checkmark:after {
  content: '';
  position: absolute;
  display: none;
}

.checkbox-label .checkbox:checked + .checkmark:after {
  display: block;
}

.checkbox-label .checkmark:after {
  left: 7px;
  top: 3px;
  width: 5px;
  height: 10px;
  border: solid #5ec1e8;
  border-width: 0 3px 3px 0;
  transform: rotate(45deg);
}

.delete-btn {
  background-color: #dc3545;
  color: #fff;
  border: none;
  padding: 5px 10px;
  border-radius: 3px;
  cursor: pointer;
}

.delete-btn:hover {
  background-color: #c82333;
}

/* Gaya baru */
.app {
  background-color: #f8b1b1;
  padding: 30px;
  font-family: 'Arial', sans-serif;
  color: #531818;
  width: 600px;
  border-radius: 30px;
  box-shadow:
    20px 20px 60px rgba(0, 0, 0, 0.1),
    -20px -20px 60px rgba(255, 255, 255, 0.5);
  margin: 0 auto; /* Membuat template berada di posisi tengah */
}

h1 {
  margin-bottom: 20px;
  font-size: 24px;
  color: #531818;
  text-align: center;
}

.input-container {
  margin-bottom: 10px;
  display: flex;
  justify-content: center;
  align-items: center;
}

.add-btn {
  background-color: #92ebf9;
  color: #531818;
  border: none;
  padding: 8px 16px;
  font-size: 14px;
  cursor: pointer;
}

.add-btn:hover {
  background-color: #d5e3f3;
}

.date-input {
  padding: 8px;
  border: 1px solid #f8a4a4;
  border-radius: 3px;
  margin-right: 10px;
}

table {
  width: 100%;
  border-collapse: collapse;
  margin-top: 20px;
}

th,
td {
  border: 1px solid #f79191;
  padding: 10px;
  text-align: left;
  vertical-align: middle; /* Menengahkan isi sel */
}

th {
  background-color: #aef2fe;
  font-weight: bold;
}

.completed {
  text-decoration: line-through;
}

.status-label {
  display: inline-block;
  padding: 2px 5px;
  background-color: #28a745;
  color: rgb(174, 237, 248);
  font-size: 12px;
  border-radius: 3px;
  margin-left: 5px;
}

/* Gaya baru */
.header {
  display: flex;
  justify-content: space-between;
  align-items: center;
  padding: 20px;
  background-color: #f9c8c8;
  color: #f1e8e8;
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

.profile-info {
  color: #3e0808;
}

.profile-info h3 {
  margin: 0;
  color: #3e0808;
}

.profile-info p {
  margin: 5px 0;
  color: #3e0808;
}

.container {
  margin: 0 auto; /* Membuat posisi relatif agar pseudo-element dapat diatur secara absolut */
}
</style>
