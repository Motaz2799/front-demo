<template>
  <nav class="navbar navbar-expand-lg navbar-dark bg-dark" style="height: 100px">
    <div class="container-fluid" style="height: 96px">
      <router-link class="navbar-brand" to="/applications">
        <img
          src="@/assets/orange-logo.svg"
          width="50"
          height="50"
          alt="Boosted - Back to Home"
          loading="lazy"
        />
      </router-link>
      <button
        class="navbar-toggler"
        type="button"
        data-bs-toggle="collapse"
        data-bs-target="#navbarNav"
        aria-controls="navbarNav"
        aria-expanded="false"
        aria-label="Toggle navigation"
      >
        <span class="navbar-toggler-icon"></span>
      </button>
      <div class="collapse navbar-collapse" id="navbarNav">
        <ul class="navbar-nav ms-auto">
          <li class="nav-item" style="height: 52px">
            <h1 class="mt-0">Cloud Assessment</h1>
          </li>
          <li class="nav-item ms-2">
            <router-link
              class="nav-link"
              to="/applications"
              :class="{ active: $route.path === '/applications' }"
              >Applications</router-link
            >
          </li>
          <li class="nav-item">
            <router-link
              class="nav-link"
              to="/servers"
              :class="{ active: $route.path === '/servers' }"
              >Servers</router-link
            >
          </li>
          <li class="nav-item">
            <router-link
              class="nav-link"
              to="/databases"
              :class="{ active: $route.path === '/databases' }"
              >Databases</router-link
            >
          </li>
          <li class="nav-item">
            <router-link
              class="nav-link"
              to="/contacts"
              :class="{ active: $route.path === '/contacts' }"
              >Contacts</router-link
            >
          </li>
          <li class="nav-item">
            <router-link
              class="nav-link"
              to="/interfaces"
              :class="{ active: $route.path === '/interfaces' }"
              >Interfaces</router-link
            >
          </li>
          <li class="nav-item">
            <router-link
              class="nav-link"
              to="/datacenters"
              :class="{ active: $route.path === '/datacenters' }"
              >Datacenters</router-link
            >
          </li>
          <li class="nav-item">
            <router-link
              class="nav-link"
              to="/environments"
              :class="{ active: $route.path === '/environments' }"
              >Environments</router-link
            >
          </li>

          <li class="nav-item" id="logout">
      <router-link class="nav-link" to="/" @click="logout">Logout</router-link>
    </li>
        </ul>
      </div>
    </div>
  </nav>
</template>

<script>
import { RouterLink } from 'vue-router'
export default {
  name: 'Navbar',
  components: {
    RouterLink
  },
  methods: {
    logout() {
      localStorage.removeItem('token');
      this.$router.push('/');
    },
  },
}
</script>

<style scoped>
#logout {
  right: 0;
}
</style>
