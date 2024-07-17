<script setup lang="ts">
import { RouterLink, RouterView, useRouter } from 'vue-router'
import { ref } from 'vue'

const pageSize = ref(2)
const router = useRouter()

const updatePageSize = (size: number) => {
  pageSize.value = size
  router.push({ name: 'event-list', query: { size: size } })
}
</script>

<template>
  <div id="layout">
    <header>
      <div class="wrapper">
        <nav>
          <RouterLink :to="{ name: 'event-list' }">Event</RouterLink> |
          <RouterLink :to="{ name: 'student' }">Student</RouterLink> |
          <RouterLink :to="{ name: 'about' }">About</RouterLink>
        </nav>
        <div>
          <label for="page-size">Events per page:</label>
          <select id="page-size" v-model="pageSize" @change="updatePageSize(pageSize)">
            <option value="2">2</option>
            <option value="5">5</option>
            <option value="10">10</option>
          </select>
        </div>
      </div>
    </header>

    <RouterView />
  </div>
</template>

<style>
#layout {
  font-family: Avenir, Helvetica, Arial, Helvetica, sans-serif;
  -webkit-font-smoothing: grayscale;
  text-align: center;
  color: #2c3e50;
}

h2 {
  font-size: 20px;
}

nav {
  padding: 30px;
}

nav a {
  font-weight: bold;
  color: #2c3e50;
}

nav a.router-link-exact-active {
  color: #42b983;
}
</style>
