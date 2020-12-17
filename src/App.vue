<template>
  <div id="app">
    <Push>
      <div class="d-flex flex-column">
        <h1>Welcome To Staff database</h1>
        <ul class="sidebar-links">
          <li class="text-white">
            <router-link class="text-white" to="/">
              <i class="fas fa-tachometer-alt mr-3"></i>
              Dashboard
            </router-link>
          </li>
          <li class="text-white">
            <router-link class="text-white" to="/list">
              <i class="fas fa-list mr-3"></i>
              Список
            </router-link>
          </li>
        </ul>
      </div>
    </Push>
    <div>
      <Navbar />
      <router-view id="page-wrap" />
    </div>
  </div>
</template>

<style lang="scss">
#app {
  font-family: Avenir, Helvetica, Arial, sans-serif;
  -webkit-font-smoothing: antialiased;
  -moz-osx-font-smoothing: grayscale;
  color: #2c3e50;
}
.bm-burger-button {
  position: fixed;
  width: 18px !important;
  height: 15px;
  left: 18px;
  top: 20px;
  z-index: 1000;
  cursor: pointer;
  color: #2c3e50;
  }
.bm-burger-bars {
  background-color: #7F7F7F;
}
.bm-menu {
  height: 100%; /* 100% Full-height */
  width: 0; /* 0 width - change this with JavaScript */
  position: fixed; /* Stay in place */
  z-index: 10001; /* Stay on top */
  top: 0;
  left: 0;
  background-color:#343A3F; /* Black*/
  overflow-x: hidden; /* Disable horizontal scroll */
  padding-top: 10px; /* Place content 60px from the top */
  transition: 0.5s; /*0.5 second transition effect to slide in the sidenav*/
  }
  .bm-item-list {
    color: #b8b7ad;
    font-size: 20px;
    margin-left: 0;
  }
  .sidebar-links {
    list-style-type: none;
    padding-left: 0;
  }
</style>

<script>
import Navbar from './components/Navbar.vue'
import { Push } from 'vue-burger-menu'
export default {
  components: {
    Push,
    Navbar
  }
}
</script>
