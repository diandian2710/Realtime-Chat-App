<template>
  <q-layout view="lHh Lpr lFf">
    <q-header elevated>
      <q-toolbar>
        <q-btn
                flat
                dense
                round
                @click="leftDrawerOpen = !leftDrawerOpen"
                aria-label="Menu"
                icon="menu"
        />

        <q-toolbar-title> Chat Room </q-toolbar-title>
      </q-toolbar>
    </q-header>

    <q-drawer
            v-model="leftDrawerOpen"
            show-if-above
            bordered
            content-class="bg-grey-2"
    >
      <q-list>
        <q-item-label header>Chat Buddies</q-item-label>
        <q-item v-for="user in users" :key="user.id">
          <q-item-section avatar>
            <q-icon name="account_circle" />
          </q-item-section>
          <q-item-section>
            <q-item-label>{{ user.name }}</q-item-label>
            <q-item-label caption>{{ user.updatedAt }}</q-item-label>
          </q-item-section>
        </q-item>
      </q-list>
    </q-drawer>

    <q-page-container>
      <ChatRoom />
    </q-page-container>
  </q-layout>
</template>

<script>
import ChatRoom from "./components/ChatRoom.vue";
import { API } from "aws-amplify";
import { listUsers } from "./graphql/queries";
import { onCreateUser } from "./graphql/subscriptions";
export default {
  name: "LayoutDefault",
  components: {
    ChatRoom,
  },
  async created() {
    const usersRes = await API.graphql({
      query: listUsers,
    });
    this.users = usersRes.data.listUsers.items;
    API.graphql({ query: onCreateUser }).subscribe({
      next: (eventData) => {
        const user = eventData.value.data.onCreateUser;
        this.users = [...this.users, user];
      },
    });
  },
  data() {
    return {
      leftDrawerOpen: false,
      users: []
    };
  },
  methods: {
  },
};
</script>

<style>
</style>
