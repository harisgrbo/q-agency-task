<template>
  <div class="navbar-wrapper">
    <img src="@/assets/logo.png" alt="">
    <nav>
      <router-link to="/">Home</router-link> |
      <router-link to="/posts">Posts</router-link>
    </nav>
  </div>
</template>

<script>
export default {
  name: 'Navbar',
  props: {
  },
}
</script>

<style scoped lang="scss">

</style>
