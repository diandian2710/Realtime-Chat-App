<template>
  <q-page class="flex">
    <Messages v-if="currentUser" v-bind:user-id="currentUser.id"/>
    <SendMessage v-if="currentUser" v-bind:user-id="currentUser.id" />

    <q-dialog v-model="showPrompt" persistent>
      <q-card style="min-width: 350px">
        <q-card-section>
          <div class="text-h6">Your name</div>
        </q-card-section>

        <q-card-section class="q-pt-none">
          <q-input dense v-model="name" autofocus @keyup.enter="createUser" />
        </q-card-section>

        <q-card-actions align="right" class="text-primary">
          <q-btn flat label="Cancel" v-close-popup />
          <q-btn flat label="Join" v-close-popup v-on:click="createUser" />
        </q-card-actions>
      </q-card>
    </q-dialog>
  </q-page>
</template>

<style>
</style>

<script>
import { API } from "aws-amplify";
import Messages from "./Messages";
import SendMessage from "./SendMessage.vue";
import { createUser } from "./../graphql/mutations";
export default {
  name: "ChatRoom",
  components: {
    Messages,
    SendMessage,
  },
  created() {
    this.showPrompt = true;
  },
  data() {
    return {
      currentUser: null,
      showPrompt: false,
      name: "",
    };
  },
  methods: {
    async createUser() {
      const { name } = this;
      if (!name) return;
      const user = { name };
      const createUserRes = await API.graphql({
        query: createUser,
        variables: { input: user },
      });
      this.currentUser = createUserRes.data.createUser;
      this.name = "";
      this.showPrompt = false;
    },
  },
};
</script>
