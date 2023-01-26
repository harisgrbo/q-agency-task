<template>
  <div class="post-list-wrapper">
    posts

    <div>
      <label>search</label>
      <input type="text" @input="applyPostTextFilter('searchTerm', $event.target.value)">
      <div>
        filters:

        <div>
          <label>Filter by author</label>
          <input type="text" @input="applyUserTextFilter('name', $event.target.value)">
        </div>
        <div>
          <label>Filter by username</label>
          <input type="text" @input="applyUserTextFilter('username', $event.target.value)">
        </div>
        <div>
          <label>Filter by email</label>
          <input type="text" @input="applyUserTextFilter('email', $event.target.value)">
        </div>
      </div>
    </div>

    <ul>
      <li v-for="post in searchedPosts" :key="post.id" @click="$router.push(`/posts/${post.id}`)">
        {{ post.title }}
        {{ post.body }}
        {{ post.user.name }}
      </li>
    </ul>
  </div>
</template>

<script>
// @ is an alias to /src

export default {
  name: 'PostList',
  components: {
  },
  data() {
    return {
      posts: [],
      users: [],
      comments: [],
      postsWithRelations: [],
      searchedPosts: [],
      userMap: {},
      postWithRelationsMap: {},
      appliedFilters: {},
    }
  },
  async created() {
    // The api does not support includes of relations/associations, so we will have to connect them ourselves, after fetching the data
    await Promise.all([
        this.fetchResources('posts'),
        this.fetchResources('users'),
        this.fetchResources('comments')
    ])

    this.relateResources();
  },
  methods: {
    applyPostTextFilter(key, value) {
      if (value.length) {
        this.appliedFilters[key] = (item) => {
          return item.body.toLowerCase().includes(value.toLowerCase()) || item.title.toLowerCase().includes(value.toLowerCase());
        }
      } else if (this.appliedFilters[key] !== undefined) {
        delete this.appliedFilters[key];
      }

      this.search();
    },
    applyUserTextFilter(key, value) {
      if (value.length) {
        this.appliedFilters[key] = (item) => {
          return item.user[key].toLowerCase().includes(value.toLowerCase());
        }
      } else if (this.appliedFilters[key] !== undefined) {
        delete this.appliedFilters[key];
      }

      this.search();
    },
    search() {
      const filters = Object.values(this.appliedFilters);

      this.searchedPosts = [...this.postsWithRelations];

      filters.forEach((predicate) => {
        this.searchedPosts = this.searchedPosts.filter(predicate);
      });
    },
    async fetchResources(resource) {
      try {
        let res = await this.axios.get(`/${resource}`);

        this[resource] = res.data;
      } catch (e) {
        console.log(e)
      }
    },
    relateResources() {
      this.posts.forEach((post, index) => {
        const postWithRelations = Object.assign({}, post);
        const userId = post.userId;

        if (this.userMap[userId] === undefined) {
          this.userMap[userId] = this.users.findIndex((user) => user.id === userId);
        }

        postWithRelations.user = this.users[this.userMap[userId]];
        postWithRelations.comments = [];

        this.postsWithRelations.push(postWithRelations);

        this.postWithRelationsMap[postWithRelations.id] = index;
      })

      this.comments.forEach((comment) => {
        const postId = comment.postId;
        const post = this.postsWithRelations[this.postWithRelationsMap[postId]];

        post.comments.push(comment);
      })

      this.searchedPosts = [...this.postsWithRelations];
    }
  }
}
</script>
