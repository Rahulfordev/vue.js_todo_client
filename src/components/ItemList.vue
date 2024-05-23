<template>
  <div class="container">
    <h2>Item List</h2>
    <ul>
      <li v-for="item in items" :key="item._id" class="item">
        {{ item.name }} - {{ item.description }}
        <button @click="deleteItem(item._id)" class="btn btn-delete">
          Delete
        </button>
        <button @click="editItem(item)" class="btn btn-edit">Edit</button>
      </li>
    </ul>
    <form @submit.prevent="addItem" class="form">
      <input v-model="newItem.name" placeholder="Name" required class="input" />
      <input
        v-model="newItem.description"
        placeholder="Description"
        required
        class="input"
      />
      <button type="submit" class="btn btn-add">Add Item</button>
    </form>
  </div>
</template>

<script>
import api from "../services/api";

export default {
  data() {
    return {
      items: [],
      newItem: {
        name: "",
        description: "",
      },
      editingItem: null,
    };
  },
  async created() {
    await this.fetchItems();
  },
  methods: {
    async fetchItems() {
      const response = await api.getItems();
      this.items = response.data;
    },
    async addItem() {
      if (this.editingItem) {
        await api.updateItem(this.editingItem._id, this.newItem);
        this.editingItem = null;
      } else {
        await api.addItem(this.newItem);
      }
      this.newItem.name = "";
      this.newItem.description = "";
      await this.fetchItems();
    },
    async deleteItem(id) {
      await api.deleteItem(id);
      await this.fetchItems();
    },
    editItem(item) {
      this.newItem.name = item.name;
      this.newItem.description = item.description;
      this.editingItem = item;
    },
  },
};
</script>

<style scoped>
.container {
  max-width: 600px;
  margin: 0 auto;
  padding: 20px;
  font-family: Arial, sans-serif;
}

h2 {
  text-align: center;
  color: #333;
}

ul {
  list-style-type: none;
  padding: 0;
}

.item {
  display: flex;
  justify-content: space-between;
  align-items: center;
  padding: 10px;
  border-bottom: 1px solid #ddd;
}

.btn {
  padding: 5px 10px;
  margin-left: 5px;
  border: none;
  border-radius: 3px;
  cursor: pointer;
}

.btn-delete {
  background-color: #e74c3c;
  color: white;
}

.btn-edit {
  background-color: #f39c12;
  color: white;
}

.btn-add {
  background-color: #2ecc71;
  color: white;
  width: 100%;
  padding: 10px;
  margin-top: 10px;
}

.input {
  width: calc(50% - 10px);
  padding: 10px;
  margin-right: 10px;
  border: 1px solid #ddd;
  border-radius: 3px;
}

.form {
  display: flex;
  flex-direction: column;
  align-items: center;
}
</style>
