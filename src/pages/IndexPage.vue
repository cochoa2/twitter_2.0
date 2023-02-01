<template>
  <q-page class="grid">
    <div class="row col q-pa-md justify-center">
      <q-card class="q-pa-md" flat bordered style="width: 482px">
        <q-input
          v-model="userPost"
          autogrow
          clearable
          placeholder="How are you feeling today?"
          ><q-btn round dense flat icon="send" @click="sendUserPost"></q-btn
        ></q-input>
      </q-card>
    </div>
    <div class="row col justify-center">
      <q-list>
        <UserPostsComponent
          v-for="post in followerPosts"
          :key="post._id"
          v-bind="post"
        />
      </q-list>
    </div>
  </q-page>
</template>

<script>
import { defineComponent, ref, onMounted } from "vue";
import UserPostsComponent from "components/UserPostsComponent.vue";
import { api } from "boot/axios";

export default defineComponent({
  name: "IndexPage",

  components: {
    UserPostsComponent,
  },

  setup() {
    const userPost = ref("");
    const followerPostsList = ref([]);
    const fetchUserPost = () =>
      api
        .post("", {
          query: `
          query GetUserPosts($amount: Int) {
            getUserPosts(amount: $amount) {
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
            amount: 25,
          },
        })
        .then((result) => {
          followerPostsList.value = result.data.data.getUserPosts;
        });

    onMounted(() => {
      fetchUserPost();
    });
    return {
      userPost,
      followerPosts: followerPostsList,

      sendUserPost() {
        api
          .post(
            "",
            {
              query: `mutation Mutation($userPostInput: UserPostInput) {
            createUserPost(UserPostInput: $userPostInput) {
              createdAt
              name
              postText
              thumbsDown
              thumbsUp
            }
          }`,
              variables: {
                userPostInput: {
                  name: "Jack O'Donnel",
                  postText: userPost.value,
                },
              },
            },
            {
              headers: {
                "Content-Type": "application/json",
              },
            }
          )
          .then((result) => {
            let tmp = [result.data.data.createUserPost];
            tmp.push(...followerPostsList.value);
            followerPostsList.value = tmp;
          });
        userPost.value = "";
      },
    };
  },
});
</script>
