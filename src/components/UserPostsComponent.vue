<template>
  <div class="row q-pa-md items-start q-gutter-md">
    <q-card class="my-card" flat bordered>
      <q-card-section horizontal>
        <q-card-section class="q-pt-xs">
          <div class="text-overline">{{ date }}</div>
          <div class="text-h5 q-mt-sm q-mb-xs">{{ userName }}</div>
          <div class="text-caption text-grey">
            {{ postText }}
          </div>
        </q-card-section>

        <!-- <q-card-section class="col-5 flex flex-center">
          <q-img
            class="rounded-borders"
            src="https://cdn.quasar.dev/img/parallax2.jpg"
          />
        </q-card-section> -->
      </q-card-section>

      <q-separator />

      <q-card-actions>
        <q-input v-model="userPost" filled autogrow clearable
          ><q-btn round dense flat icon="send" @click="dothings"></q-btn
        ></q-input>
      </q-card-actions>
    </q-card>
  </div>
</template>

<script>
import { defineComponent, ref } from "vue";

export default defineComponent({
  name: "UserPostsComponent",
  props: {
    userName: {
      type: String,
      required: true,
    },

    date: {
      type: String,
      required: true,
    },

    postText: {
      type: String,
      required: true,
    },
  },
  setup() {
    const userPost = ref("");
    return {
      userPost,

      dothings() {
        console.log(userPost.value);
        userPost.value = "";
      },
    };
  },
});
</script>
