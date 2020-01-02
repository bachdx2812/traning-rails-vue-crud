<template>
  <div>
    <h1>Users List</h1>
    <table>
      <tr>
        <td>ID</td>
        <td>Name</td>
        <td>Description</td>
        <td>Action</td>
      </tr>
      <tr v-for="user in users" :key="user.id">
        <td>{{ user.id }}</td>
        <td>{{ user.name }}</td>
        <td>{{ user.description }}</td>
        <td>
          <button @click="editUser(user.id)">Edit</button>
        </td>
      </tr>
    </table>

    <div id="myModal" class="modal" v-if="modalDisplay">
      <!-- Modal content -->
      <div class="modal-content">
        <span class="close" @click="closeModal">&times;</span>
        <p>
          Name:
          <input v-model="editingUser.name" />
          Description:
          <input v-model="editingUser.description" />

          <button @click="updateUser">Update</button>
        </p>
      </div>
    </div>
  </div>
</template>

<script>
import axios from "axios";
const tokenElement = document.querySelector("meta[name=csrf-token]");

export default {
  data: function() {
    return {
      users: [],
      modalDisplay: false,
      editingUser: null
    };
  },
  created: async function() {
    const res = await axios.get("/users.json");

    this.users = res.data;
  },
  methods: {
    editUser: async function(userId) {
      const res = await axios.get(`/users/${userId}/edit`);
      this.editingUser = res.data;

      this.modalDisplay = true;
    },
    updateUser: async function() {
      const res = await axios.put(
        `/users/${this.editingUser.id}`,
        {
          user: this.editingUser
        },
        {
          headers: {
            "X-Requested-With": "XMLHttpRequest",
            "X-CSRF-TOKEN": tokenElement ? tokenElement.content : null
          }
        }
      );

      const user = res.data;

      const editedIndex = this.users.findIndex(e => e.id === this.editingUser.id)
      this.$set(this.users, editedIndex, user)
    },
    closeModal: function() {
      this.updatingUser = null
      this.modalDisplay = false
    }
  }
};
</script>

<style>
.modal {
  /* display: block; */
  position: fixed; /* Stay in place */
  z-index: 1; /* Sit on top */
  left: 0;
  top: 0;
  width: 100%; /* Full width */
  height: 100%; /* Full height */
  overflow: auto; /* Enable scroll if needed */
  background-color: rgb(0, 0, 0); /* Fallback color */
  background-color: rgba(0, 0, 0, 0.4); /* Black w/ opacity */
}

/* Modal Content/Box */
.modal-content {
  background-color: #fefefe;
  margin: 15% auto; /* 15% from the top and centered */
  padding: 20px;
  border: 1px solid #888;
  width: 80%; /* Could be more or less, depending on screen size */
}

/* The Close Button */
.close {
  color: #aaa;
  float: right;
  font-size: 28px;
  font-weight: bold;
}

.close:hover,
.close:focus {
  color: black;
  text-decoration: none;
  cursor: pointer;
}
</style>