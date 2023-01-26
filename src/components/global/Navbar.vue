<template>
  <div class="navbar-wrapper">
    <img class="navbar-wrapper__logo" src="@/assets/logo.png" alt="">
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
.navbar-wrapper {
  display: flex;
  align-items: center;
  justify-content: flex-start;
  padding: 24px;
  position: fixed;
  top: 0;
  left: 0;
  right: 0;
  background: #fff;
  box-shadow: 1px 6px 19px 0px rgba(0,0,0,0.21);
  -webkit-box-shadow: 1px 6px 19px 0px rgba(0,0,0,0.21);
  -moz-box-shadow: 1px 6px 19px 0px rgba(0,0,0,0.21);
  height: fit-content;
  z-index: 1;

  &__logo {
    height: 40px;
    margin-right: 24px;
  }
}
</style>
