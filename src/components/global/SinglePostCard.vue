<template>
  <div class="post-wrapper" @click="$router.push(`/posts/${id}`)">
    <img src="@/assets/300x300.png" class="post-wrapper__image" alt="">
    <div class="post-wrapper__body">
      <h4 class="post-wrapper__body-title">{{ title }}</h4>
      <p class="post-wrapper__body-content">{{ content }}</p>
    </div>
    <span class=post-wrapper__author>{{ 'Author: ' + author }}</span>
    <button class="post-wrapper__button" @click.prevent.stop="$emit('show-comments')">Show comments</button>
  </div>
</template>

<script>
export default {
  name: "SinglePostCard",
  props: ['title', 'content', 'id', 'author'],
}
</script>

<style scoped lang="scss">
.post-wrapper {
  display: flex;
  flex-direction: column;
  border-radius: 10px;
  box-shadow: 1px 6px 19px 0px rgba(0,0,0,0.10);
  -webkit-box-shadow: 1px 6px 19px 0px rgba(0,0,0,0.10);
  -moz-box-shadow: 1px 6px 19px 0px rgba(0,0,0,0.10);
  align-items: flex-start;
  justify-content: flex-start;
  position: relative;
  overflow: hidden;
  transition: 0.3s all ease;

  &__author {
    position: absolute;
    top: 12px;
    right: 12px;
    width: fit-content;
    font-size: 10px;
    font-weight: 600;
    text-transform: uppercase;
    background: #fff;
    color: #000;
    border-radius: 4px;
    padding: 3px 8px;
    display: flex;
    align-items: center;
  }

  &__button {
    height: 38px;
    background: #ff1f1f;
    color: #fff;
    display: flex;
    align-items: center;
    justify-content: center;
    outline: none;
    border: none;
    border-radius: 4px;
    width: fit-content;
    cursor: pointer;
    font-weight: bold;
    margin: 0 auto 16px auto;
    box-sizing: border-box;
    padding: 0 12px;
  }

  &:hover {
    box-shadow: 1px 6px 19px 0px rgba(0,0,0,0.21);
    -webkit-box-shadow: 1px 6px 19px 0px rgba(0,0,0,0.21);
    -moz-box-shadow: 1px 6px 19px 0px rgba(0,0,0,0.21);
    cursor: pointer;
  }

  &__image {
    display: flex;
    height: 200px;
    width: 100%;
    object-fit: cover;
  }

  &__body {
    padding: 24px;
    text-align: left;
    display: flex;
    flex-direction: column;
    align-items: flex-start;
    justify-content: flex-start;

    &-title {
      overflow: hidden;
      text-overflow: ellipsis;
      display: -webkit-box;
      -webkit-line-clamp: 1; /* number of lines to show */
      line-clamp: 1;
      -webkit-box-orient: vertical;
      margin: 0;
    }

    &-content {
      overflow: hidden;
      text-overflow: ellipsis;
      display: -webkit-box;
      -webkit-line-clamp: 2; /* number of lines to show */
      line-clamp: 2;
      -webkit-box-orient: vertical;
    }
  }
}
</style>