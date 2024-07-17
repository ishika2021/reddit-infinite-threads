<template>
  <div class="header">
    <span
      v-for="(bullet, idx) in bullets"
      :key="idx"
      :class="bullet"
      class="bullet"
    />
  </div>
  <div class="card">
    <div class="content">
      <img :src="contentImage" height="150px" />
    </div>
    <div class="comments">
      <Comment
        v-for="(comment, idx) in comments"
        :key="idx"
        :id="comment.id"
        :children="comment.children"
        :handleReply="handleReply"
      >
        {{ comment.text }}
      </Comment>
    </div>
    <div class="input" :class="parentID !== null ? 'isActive' : ''">
      <input
        type="text"
        v-model="modelValue"
        placeholder="Enter the comment..."
        @keydown.enter="handleCommentInput()"
      />
    </div>
  </div>
</template>

<script>
import Comment from "./Comment.vue";
import { ref, computed, watch } from "vue";
import post from "../assets/post.jpg";
import { v4 as uuidv4 } from "uuid";
export default {
  name: "Card",
  components: { Comment },
  setup() {
    const bullets = ref(["red", "green", "yellow"]);
    const contentImage = post;
    const comments = ref([
      {
        id: "1",
        text: "This is the first comment",
        children: [
          {
            id: "1.1",
            text: "This is the first  nested comment",
            children: [],
          },
        ],
      },
    ]);
    const modelValue = ref("");
    const parentID = ref(null);

    const handleCommentInput = () => {
      const commentID = uuidv4();
      const obj = {
        id: commentID,
        text: modelValue.value,
        children: [],
      };

      if (!parentID.value) {
        comments.value.push(obj);
      } else {
        const targetObj = updateComment(comments.value, parentID.value);
        if (targetObj) {
          targetObj.children.push(obj);
        }
      }
      parentID.value = null;
      modelValue.value = "";
    };

    const updateComment = (comments, targetID) => {
      for (const item of comments) {
        if (item.id === targetID) {
          return item;
        } else if (item.children.length > 0) {
          const result = updateComment(item.children, targetID);
          if (result) {
            return result;
          }
        }
      }
    };

    const handleReply = (id) => {
      parentID.value = id;
    };

    return {
      bullets,
      contentImage,
      modelValue,
      comments,
      handleCommentInput,
      handleReply,
      parentID,
    };
  },
};
</script>

<style scoped lang="scss">
.header {
  display: flex;
  justify-content: flex-end;
  border: 1px solid #e8e8e847;
  gap: 5px;
  border-radius: 6px 6px 0px 0px;
  padding: 3px 5px;
}
.card {
  border: 1px solid #e8e8e847;
  border-radius: 0px 0px 6px 6px;
  padding: 20px;
  display: grid;
  gap: 5px;
}
.red {
  background: #f7685b;
}
.yellow {
  background: #fdb93e;
}
.green {
  background: #2dbd96;
}
.bullet {
  height: 15px;
  width: 15px;
  border-radius: 50px;
}
.input {
  border: 1px solid #525256;
  height: 20px;
  display: flex;
  gap: 10px;
  border-radius: 6px;
  padding: 5px;
}
input {
  outline: none;
  font-size: 14px;
  width: 100%;
  border: none;
  background: transparent;
  font-weight: 400;
}
.comments {
  display: grid;
  gap: 5px;
}
.isActive {
  border: 1.5px solid #464da8fa;
}
</style>
