<template>
  <div id="app">
    <h1>User Management</h1>
    <form @submit.prevent="addUser">
      <input v-model="name" type="text" placeholder="Name" required />
      <input v-model="email" type="email" placeholder="Email" required />
      <button type="submit">Add User</button>
    </form>
    <p v-if="error" style="color: red;">{{ error }}</p>
    <h2>Users List</h2>
    <ul>
      <li v-for="user in users" :key="user.id">{{ user.name }} - {{ user.email }}</li>
    </ul>
  </div>
</template>

<script>
export default {
  data() {
    return {
      name: '',
      email: '',
      users: [],
      error: '',
    };
  },
  methods: {
    async fetchUsers() {
      try {
        const response = await fetch('http://localhost:8080/api/v1/queue');
        this.users = await response.json();
      } catch (err) {
        console.error('Error fetching users:', err);
      }
    },
    async addUser() {
      try {
        const response = await fetch('http://localhost:8080/api/v1/adduser', {
          method: 'POST',
          headers: {
            'Content-Type': 'application/json',
          },
          body: JSON.stringify({
            name: this.name,
            email: this.email,
          }),
        });

        if (response.ok) {
          this.name = '';
          this.email = '';
          await this.fetchUsers();
        } else {
          throw new Error('Failed to add user');
        }
      } catch (err) {
        console.error('Error adding user:', err);
        this.error = 'Failed to add user. Please try again.';
      }
    },
  },
  mounted() {
    this.fetchUsers();
    setInterval(this.fetchUsers, 5000);
  },
};
</script>

<style>
form {
  margin-bottom: 1em;
}
input {
  margin-right: 0.5em;
}
</style>
