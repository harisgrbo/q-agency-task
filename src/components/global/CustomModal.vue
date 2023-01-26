<template>
  <div class="custom-modal-wrapper">
    <div class="custom-modal-wrapper__inner">
      <div class="custom-modal-wrapper__inner-header">
        <h2>Comments</h2>
        <button @click="$emit('close-modal')">
          <svg xmlns="http://www.w3.org/2000/svg" fill="none" viewBox="0 0 24 24" stroke-width="1.5" stroke="currentColor" class="w-6 h-6">
            <path stroke-linecap="round" stroke-linejoin="round" d="M6 18L18 6M6 6l12 12" />
          </svg>
        </button>
      </div>
      <div class="custom-modal-wrapper__inner-content">
        <SingleComment v-for="comment in comments" :key="comment.id" :body="comment.body" :email="comment.email" :name="comment.name"></SingleComment>
      </div>
    </div>
  </div>
</template>

<script>
import SingleComment from "@/components/global/SingleComment";
export default {
  name: "CustomModal",
  components: {SingleComment},
  props: ['comments']
}
</script>

<style scoped lang="scss">
@mixin for-phone-only {
  @media (max-width: 599px) {
    @content;
  }
}

.custom-modal-wrapper {
  position: fixed;
  top: 0;
  left: 0;
  right: 0;
  bottom: 0;
  display: flex;
  flex-direction: column;
  align-items: center;
  justify-content: center;
  background: rgba(0, 0, 0, 0.35);
  z-index: 10;

  &__inner {
    border-radius: 10px;
    background: #fff;
    width: 60vw;
    height: 60vh;
    padding: 24px;
    overflow-y: scroll;
    box-sizing: border-box;

    @include for-phone-only {
      height: 100%;
      width: 100%;
    }

    &-header {
      font-size: 18px;
      display: flex;
      align-items: center;
      justify-content: space-between;
      padding-bottom: 24px;
      border-bottom: 1px solid #ddd;
      margin-bottom: 24px;

      h2 {
        margin: 0;
      }

      button {
        height: 30px;
        width: 30px;
        border-radius: 6px;
        background: #f1f1f1;
        outline: none;
        border: none;
        cursor: pointer;
      }
    }

    ::v-deep .single-comment-wrapper {
      margin-bottom: 24px;

      &:last-child {
        margin-bottom: 0;
      }
    }
  }
}
</style>