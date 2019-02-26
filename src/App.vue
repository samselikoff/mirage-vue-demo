<template>
  <div id="app">
    <h1>Hello, Mirage!</h1>
    <p>Here are the users:</p>

    <div v-if="isLoading">
      Loading...
    </div>
    <div v-else>
      <div v-for="user in users" :key="user.id">
        {{ user.attributes.name }}
      </div>
    </div>
  </div>
</template>

<script>
import axios from 'axios';
import Server, { Model, Factory, JSONAPISerializer } from '@miragejs/server';

new Server({
  models: {
    user: Model
  },
  factories: {
    user: Factory.extend({
      name(i) {
        return `User ${i}`;
      }
    })
  },
  serializers: {
    application: JSONAPISerializer
  },
  scenarios: {
    default(server) {
      server.createList('user', 10);
    }
  },
  baseConfig() {
    this.namespace = 'api';
    this.timing = 600;

    this.get('/users');

    this.passthrough('http://localhost:8080/**');
  }
});

export default {
  name: 'app',
  data () {
    return {
      users: null,
      isLoading: true
    }
  },
  mounted () {
    axios.get('/api/users').then(response => {
      this.users = response.data.data;
      this.isLoading = false;
    });
  }
}
</script>

<style>
#app {
  font-family: 'Avenir', Helvetica, Arial, sans-serif;
  -webkit-font-smoothing: antialiased;
  -moz-osx-font-smoothing: grayscale;
  text-align: center;
  color: #2c3e50;
  margin-top: 60px;
}
</style>
