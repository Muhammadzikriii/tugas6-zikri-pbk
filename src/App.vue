<template>
  <div class="container">
    <h1>Data Nama Pemain Bola</h1>
    <input v-model="newTitle" placeholder="Title" />
    <textarea v-model="newContent" placeholder="Content"></textarea>
    <button @click="addData">Save</button>
    <div v-for="(item, index) in items" :key="item.id" class="item">
      <p>{{ item.title }}</p>
      <p>{{ item.content }}</p>
      <button @click="editData(index)">Edit</button>
      <button @click="deleteData(item.id)">Delete</button>
    </div>
    <button @click="loadData">Load</button>
  </div>
</template>

<script>
import axios from 'axios';

export default {
  data() {
    return {
      newTitle: '',
      newContent: '',
      items: [],
      editingIndex: -1
    };
  },
  methods: {
    async loadData() {
      try {
        const response = await axios.get('http://localhost:3000/articles');
        this.items = response.data.filter(item => item.id !== null); // Filter out any item with null id
      } catch (error) {
        console.error('Error loading data:', error);
      }
    },
    async addData() {
      try {
        if (this.editingIndex === -1) {
          const response = await axios.post('http://localhost:3000/articles', {
            title: this.newTitle,
            content: this.newContent
          });
          this.items.push(response.data);
        } else {
          const item = this.items[this.editingIndex];
          item.title = this.newTitle;
          item.content = this.newContent;
          await axios.put(`http://localhost:3000/articles/${item.id}`, item);
          this.editingIndex = -1;
        }
        this.newTitle = '';
        this.newContent = '';
      } catch (error) {
        console.error('Error adding data:', error);
      }
    },
    editData(index) {
      this.newTitle = this.items[index].title;
      this.newContent = this.items[index].content;
      this.editingIndex = index;
    },
    async deleteData(id) {
      try {
        await axios.delete(`http://localhost:3000/articles/${id}`);
        this.items = this.items.filter(item => item.id !== id);
      } catch (error) {
        console.error('Error deleting data:', error);
      }
    }
  },
  mounted() {
    this.loadData();
  }
};
</script>

<style scoped>
.container {
  background-color: #151515;
  color: white;
  padding: 20px;
  border-radius: 5px;
  width: 300px;
  margin: 0 auto;
}

input, textarea {
  display: block;
  width: 100%;
  margin-bottom: 10px;
  padding: 10px;
  border-radius: 5px;
  border: 1px solid #ccc;
}

button {
  display: block;
  width: 100%;
  padding: 10px;
  border: none;
  border-radius: 5px;
  margin-bottom: 10px;
  cursor: pointer;
  font-size: 16px;
}

button:nth-child(3) {
  background-color: #4CAF50;
  color: white;
}

button:nth-child(4), button:nth-child(5) {
  background-color: #FF5722;
  color: white;
  margin-top: 5px;
}

.item {
  background-color: #222;
  padding: 10px;
  border-radius: 5px;
  margin-top: 10px;
  border: 1px solid #444;
}
</style>
