<template>
  <div class="row q-pa-md justify-center">
    <q-card flat bordered style="width: 482px">
      <q-card-section class="q-pa-sm">
        <div class="text-overline text-orange-9">
          {{ formatDate(createdAt) }}
        </div>
        <div class="text-h5 q-mt-sm q-mb-xs">{{ name }}</div>
        <div class="q-mt-sm q-mb-xs" style="word-wrap: normal">
          {{ postText }}
        </div>
      </q-card-section>

      <div class="q-pa-xs">
        <q-chip
          clickable
          @click="clickThumbsUp"
          color="primary"
          text-color="white"
          icon="thumb_up"
        >
          {{ localThumbsUp }}
        </q-chip>
        <q-chip
          clickable
          @click="clickThumbsDown"
          color="orange"
          text-color="white"
          icon="thumb_down"
        >
          {{ localThumbsDown }}
        </q-chip>
      </div>

      <q-separator />
      <div v-if="localComments" class="q-pa-md">
        <div
          style="width: 100%"
          v-for="comment in localComments"
          :key="comment._id"
          v-bind="comment"
        >
          <q-chat-message
            v-if="comment.name == 'Jack O\'Donnel'"
            sent
            :text="[comment.postText]"
            bg-color="primary"
            text-color="white"
          />
          <q-chat-message
            v-else
            :name="comment.name"
            :text="[comment.postText]"
            bg-color="grey-2"
          />
        </div>
      </div>

      <q-input
        class="q-px-sm q-pb-sm"
        v-model="userPost"
        autogrow
        clearable
        placeholder="Write a comment..."
        ><q-btn round dense flat icon="send" @click="submitComment"></q-btn
      ></q-input>
    </q-card>
  </div>
</template>

<script>
import { defineComponent, ref } from "vue";
import { api } from "boot/axios";

export default defineComponent({
  name: "UserPostsComponent",
  props: {
    _id: {
      type: String,
      required: true,
    },

    name: {
      type: String,
      required: true,
    },

    createdAt: {
      type: String,
      required: true,
    },

    postText: {
      type: String,
      required: true,
    },

    thumbsUp: {
      type: Number,
      required: true,
    },

    thumbsDown: {
      type: Number,
      required: true,
    },

    comments: {
      type: Array,
    },
  },
  setup(props) {
    const userPost = ref("");
    const localThumbsUp = ref(props.thumbsUp);
    const localThumbsDown = ref(props.thumbsDown);
    const localComments = ref(props.comments);
    const formatDate = (some_date) => {
      const options = { year: "numeric", month: "long", day: "numeric" };
      return new Date(some_date).toLocaleDateString(undefined, options);
    };
    const clickAction = (like_variable) => {
      return api.post("", {
        query: `
            mutation Mutation($id: ID!, $userLikeInput: UserLikeInput) {
              editUserLike(ID: $id, UserLikeInput: $userLikeInput)
            }`,
        variables: like_variable,
      });
    };
    const clickThumbsUp = () => {
      clickAction({
        id: props._id,
        userLikeInput: {
          thumbsUp: localThumbsUp.value + 1,
        },
      }).then((result) => {
        if (result.data.data.editUserLike == true) {
          localThumbsUp.value = localThumbsUp.value + 1;
        }
      });
    };
    const clickThumbsDown = () => {
      clickAction({
        id: props._id,
        userLikeInput: {
          thumbsDown: localThumbsDown.value + 1,
        },
      }).then((result) => {
        if (result.data.data.editUserLike == true) {
          localThumbsDown.value = localThumbsDown.value + 1;
        }
      });
    };
    const submitComment = () => {
      return api
        .post("", {
          query: `
        mutation Mutation($id: ID!, $userPostInput: UserPostInput) {
          createCommentUserPost(ID: $id, UserPostInput: $userPostInput) {
            _id
            createdAt
            name
            postText
            thumbsDown
            thumbsUp
            comments {
              _id
              createdAt
              name
              postText
              thumbsDown
              thumbsUp
            }
          }
        }
        `,
          variables: {
            id: props._id,
            userPostInput: {
              name: "Jack O'Donnel",
              postText: userPost.value,
            },
          },
        })
        .then((result) => {
          localComments.value.push(result.data.data.createCommentUserPost);
          userPost.value = "";
        });
    };
    return {
      userPost,
      localThumbsUp,
      localThumbsDown,
      localComments,

      formatDate,
      clickThumbsUp,
      clickThumbsDown,
      submitComment,
    };
  },
});
</script>
