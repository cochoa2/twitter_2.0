<template>
  <q-page class="grid">
    <div class="row col justify-center">
      <q-card class="my-card q-pa-md q-ma-md" flat bordered>
        <q-card-section horizontal>
          <q-icon
            name="record_voice_over"
            class="q-pa-sm justify-center"
          ></q-icon>
          <q-input
            v-model="userPost"
            filled
            autogrow
            clearable
            placeholder="How are you feeling today?"
            ><q-btn round dense flat icon="send" @click="sendUserPost"></q-btn
          ></q-input>
        </q-card-section>
      </q-card>
    </div>
    <div class="row col justify-center">
      <q-list>
        <UserPostsComponent
          v-for="post in followerPosts"
          :key="post.postText"
          v-bind="post"
        />
      </q-list>
    </div>
  </q-page>
</template>

<script>
import { defineComponent, ref } from "vue";
import UserPostsComponent from "components/UserPostsComponent.vue";

const followerPostsList = [
  {
    userName: "Carlos Ochoa",
    date: "2023-01-24",
    postText: "I really want to land this job!",
  },
  {
    userName: "Jordan Ochoa",
    date: "2023-01-24",
    postText: "I wish Carlos would stop talking about wanting a job....",
  },
  {
    userName: "Nugget Ochoa",
    date: "2023-01-23",
    postText: "I really want to fk up Noah!!!!",
  },
  {
    userName: "Veronica Ochoa",
    date: "2023-01-22",
    postText: "Umm Umm Ummmmmmmmmmm",
  },
];

export default defineComponent({
  name: "IndexPage",

  components: {
    UserPostsComponent,
  },

  setup() {
    const userPost = ref("");
    return {
      userPost,
      followerPosts: followerPostsList,

      sendUserPost() {
        console.log(userPost.value);
        userPost.value = "";
      },
    };
  },
});
</script>
