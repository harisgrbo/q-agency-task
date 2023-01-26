<template>
  <div class="post-list-wrapper">
    <div class="post-list-wrapper__search-wrapper">
      <div class="post-list-wrapper__search-wrapper-inner">
        <h2 class="section-title">Search</h2>
        <input type="text" @input="applyPostTextFilter('searchTerm', $event.target.value)" placeholder="Type something to search...">
        <div class="post-list-wrapper__search-wrapper-filters">
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
    </div>
    <div class="post-list-wrapper__grid" v-if="loaded">
      <SinglePostCard v-for="post in searchedPosts" :key="post.id" :id="post.id" :title="post.title" :content="post.body" :author="post.user.name" @show-comments="handleCommentsPreview(post.comments)"></SinglePostCard>
    </div>
    <div class="post-list-wrapper__loader" v-else>
      <img src="@/assets/loader.svg" alt="">
    </div>
    <CustomModal v-if="showPostCommentsModal" :comments="selectedPostComments" @close-modal="handleCloseModal"></CustomModal>
  </div>
</template>

<script>

import SinglePostCard from "@/components/global/SinglePostCard";
import CustomModal from "@/components/global/CustomModal";
export default {
  name: 'PostList',
  components: {
    CustomModal,
    SinglePostCard
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
      loaded: false,
      selectedPostComments: [],
      showPostCommentsModal: false,
    }
  },
  async created() {
    // The api does not support includes of relations/associations, so we will have to connect them ourselves, after fetching the data
    this.loaded = false;
    await Promise.all([
        this.fetchResources('posts'),
        this.fetchResources('users'),
        this.fetchResources('comments')
    ])

    this.relateResources();
    this.loaded = true;
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
    },
    handleCommentsPreview(comments) {
      this.selectedPostComments = comments;

      this.showPostCommentsModal = true;
    },
    handleCloseModal() {
      this.showPostCommentsModal = false;

      this.selectedPostComments = [];
    }
  }
}
</script>

<style lang="scss" scoped>
@mixin for-phone-only {
  @media (max-width: 599px) {
    @content;
  }
}

.post-list-wrapper {
  display: flex;
  flex-direction: column;
  align-items: flex-start;
  justify-content: flex-start;

  &__search-wrapper {
    width: 100%;
    height: fit-content;
    background: #f9f9f9;
    margin-bottom: 48px;
    padding: 36px 24px;
    box-sizing: border-box;


    @include for-phone-only {
      padding: 36px 0;
    }

    &-inner {
      display: flex;
      flex-direction: column;
      margin: 0 auto;
      align-items: flex-start;
      box-sizing: border-box;


      @include for-phone-only {
        width: 100%;
        padding: 0 16px;
      }
    }

    &-filters {
      display: grid;
      grid-template-columns: repeat(3, 1fr);
      gap: 24px;
      margin-top: 24px;
      width: 100%;

      @include for-phone-only {
        grid-template-columns: repeat(1, 1fr) !important;
      }

      > div {
        display: flex;
        flex-direction: column;
        align-items: flex-start;
        justify-content: flex-start;
        width: 100%;

        label {
          font-size: 13px;
          font-weight: 600;
          text-transform: uppercase;
          margin-bottom: 8px;
        }
      }
    }

    input {
      height: 48px;
      border-radius: 2px;
      border: none;
      width: 100%;
      background: #fff;
      font-size: 13px;
      padding: 0 12px;
      box-sizing: border-box;
      font-weight: 500;
      border-bottom: 2px solid #f1f1f1;

      &:focus {
        outline: none;
      }
    }
  }

  &__grid {
    display: grid;
    grid-template-columns: repeat(auto-fill,minmax(233px,1fr));
    gap: 24px;
    justify-content: center;
    margin: 0 auto;
    padding: 0 24px;
    width: 100%;
    box-sizing: border-box;

    @include for-phone-only {
      grid-template-columns: repeat(1, 1fr);
      width: 100%;
      padding: 0 16px;
      box-sizing: border-box;
    }
  }

  &__loader {
    display: flex;
    align-items: center;
    justify-content: center;
    width: 100%;

    img {
      height: 60px;
    }
  }

  .section-title {
    font-size: 25px;
    font-weight: 600;
    margin-top: 0;
  }
}
</style>
