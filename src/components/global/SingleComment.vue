<template>
  <div class="single-comment-wrapper">
    <h2>{{ name + ' by ' + email }}</h2>
    <p>{{ body }}</p>
  </div>
</template>

<script>
export default {
  name: "SingleComment",
  props: ['email', 'name', 'body']
}
</script>

<style scoped lang="scss">
.single-comment-wrapper {
  display: flex;
  flex-direction: column;
  border-radius: 10px;
  box-shadow: 1px 6px 19px 0px rgba(0,0,0,0.10);
  -webkit-box-shadow: 1px 6px 19px 0px rgba(0,0,0,0.10);
  -moz-box-shadow: 1px 6px 19px 0px rgba(0,0,0,0.10);
  align-items: flex-start;
  justify-content: flex-start;
  transition: 0.3s all ease;
  padding: 16px;
  text-align: left;

  &:hover {
    box-shadow: 1px 6px 19px 0px rgba(0,0,0,0.21);
    -webkit-box-shadow: 1px 6px 19px 0px rgba(0,0,0,0.21);
    -moz-box-shadow: 1px 6px 19px 0px rgba(0,0,0,0.21);
    cursor: pointer;
  }

  h2 {
    font-size: 18px;

  }
}
</style>