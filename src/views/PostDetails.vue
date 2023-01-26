<template>
  <div class="post-details-wrapper">
    <div class="post-details-wrapper__inner" v-if="loaded">
      <h2>{{ post.body }}</h2>
      <p>{{ 'Post Author: ' + user.name }}</p>
      <h2 class="comments">Comments</h2>
      <div class="post-details-wrapper__inner-comments">
        <SingleComment v-for="comment in comments" :key="comment.id" :body="comment.body" :email="comment.email" :name="comment.name"></SingleComment>
      </div>
    </div>
    <div v-else class="post-details-wrapper__loader">
      <img src="@/assets/loader.svg" alt="">
    </div>
  </div>
</template>

<script>
import SingleComment from "@/components/global/SingleComment";
export default {
  name: "PostDetails",
  components: {SingleComment},
  data() {
    return {
      post: null,
      user: null,
      comments: [],
      loaded: false,
    }
  },
  async created() {
    this.loaded = false,
    await this.fetchSinglePost();
    await Promise.all([
        this.fetchUser(),
        this.fetchComments()
    ])
    this.loaded = true;
  },
  methods: {
    async fetchSinglePost() {
      try {
        let res = await this.axios.get(`/posts/${this.$route.params.id}`);

        this.post = res.data;
      } catch(e) {
        console.log(e)
      }
    },
    async fetchUser() {
      try {
        let res = await this.axios.get(`/users/${this.post.userId}`);

        this.user = res.data;
      } catch(e) {
        console.log(e)
      }
    },
    async fetchComments() {
      try {
        let res = await this.axios.get(`/posts/${this.$route.params.id}/comments`);

        this.comments = res.data;
      } catch(e) {
        console.log(e)
      }
    }
  }
}
</script>

<style scoped lang="scss">
@mixin for-phone-only {
  @media (max-width: 599px) {
    @content;
  }
}

.post-details-wrapper {
  background: #f9f9f9;
  display: flex;
  align-items: center;
  justify-content: center;
  padding: 60px 0;

  @include for-phone-only {
    padding: 36px 0;
    background: #fff;
  }

  &__inner {
    width: 60%;
    padding: 24px;
    background: #fff;
    display: flex;
    flex-direction: column;
    align-items: flex-start;
    justify-content: flex-start;

    @include for-phone-only {
      width: 100%;
    }

    h2 {
      text-align: left;
      margin-top: 0;
    }

    p {
      font-weight: bold;
    }

    ::v-deep .single-comment-wrapper {
      margin-bottom: 24px;
    }
  }

  &__loader {
    display: flex;
    align-items: center;
    justify-content: center;
    width: 100%;
    min-height: 500px;

    img {
      height: 60px;
    }
  }
}
</style>