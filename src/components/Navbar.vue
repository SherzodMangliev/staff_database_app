<template>
  <div class="navbar navbar-light w-100 bg-white shadow-sm justify-content-start py-3 position-fixed top-navbar">
    <div class="mx-3">
    </div>
    <div class="mx-4">
      <router-link class="navbar-link text-white font-weight-light bg-primary p-2 rounded" to="/">
        <i class="fas fa-tachometer-alt mr-1"></i>
        Dashboard
      </router-link>
    </div>
    <div class="mx-4">
      <router-link class="navbar-link bg-info p-2 rounded text-white" to="/list">
        <i class="fas fa-list mr-1"></i>
        Список
      </router-link>
    </div>
  </div>
</template>

<script>
export default {
  name: 'Navbar',
  methods: {
    sideOpen () {
      this.$emit('clicked')
    }
  }

}
</script>

<style scoped>
  .top-navbar {
    z-index: 990;
  }
  .navbar-link {
    color: #7F7F7F;
  }
</style>
