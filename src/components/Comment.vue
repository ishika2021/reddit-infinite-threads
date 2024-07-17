<script>
import { ref, h, watch, defineComponent } from "vue";
import { VIcon } from "vuetify/components";

const Comment = defineComponent({
  name: "Comment",
  props: {
    id: {
      type: String,
      default: "",
    },
    children: {
      type: Array,
      default: () => [],
    },
    handleReply: {
      type: Function,
      default: () => {},
    },
  },
  setup(props, { emit, slots }) {
    const count = ref(0);
    const slot = slots.default ? slots.default() : [];
    const handleUpvote = () => {
      count.value++;
    };

    const handleDownvote = () => {
      count.value--;
    };

    const handleCommentReply = (id) => {
      props.handleReply(id);
    };

    // To handle recursive calling of Comment in case of nested comments
    const renderChildren = (children) => {
      return children.map((child) =>
        h(
          Comment,
          {
            id: child.id,
            children: child.children,
            handleReply: props.handleReply,
          },
          { default: () => child.text },
        ),
      );
    };

    return () =>
      h("div", { class: "comment" }, [
        h("div", { class: "header" }, [
          h("span", { class: "avatar" }, "A"),
          h("span", { class: "username" }, "Anonymous"),
        ]),
        slot.map((child) => {
          return h("div", { class: "message" }, [child]);
        }),

        ...renderChildren(props.children),
        h("div", { class: "footer" }, [
          h(VIcon, {
            icon: "mdi-reply",
            onClick: () => handleCommentReply(props.id),
          }),
          h(VIcon, {
            icon: "mdi-arrow-up-bold",
            onClick: handleUpvote,
          }),
          h("span", count.value),
          h(VIcon, {
            icon: "mdi-arrow-down-bold",
            onClick: handleDownvote,
          }),
        ]),
      ]);
  },
});

export default Comment;
</script>

<style scoped>
.comment {
  border: 1px solid #e8e8e847;
  padding: 10px;
  display: grid;
  gap: 5px;
  font-size: 12px;
  border-radius: 4px;
}
.header {
  display: flex;
  gap: 5px;
  align-items: center;
  font-size: 14px;
}
.avatar {
  height: 20px;
  width: 20px;
  border-radius: 50px;
  background: grey;
  text-align: center;
}
.message {
  margin-left: 10px;
  text-align: left;
  margin-top: 5px;
}
.footer {
  height: 20px;
  display: flex;
  gap: 5px;
  justify-content: flex-end;
}
</style>
