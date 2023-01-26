<template>
  <div v-if="loaded">
    {{ post.body }}
    {{ user.name }}

    <div v-for="comment in comments" :key="comment.id">
      {{
      comment
      }}
    </div>
  </div>
</template>

<script>
export default {
  name: "PostDetails",
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

<style scoped>

</style>