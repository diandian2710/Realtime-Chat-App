<template>
  <div class="full-width row justify-center">
    <div class="col-11">
      <q-chat-message
              :text="[message.content]"
              :name="message.user.name"
              :sent="message.userId !== userId"
              v-for="message in messages"
              :key="message.id"
              :stamp="message.updatedAt"
      />
    </div>
  </div>
</template>

<style>
</style>

<script>
import { API } from "aws-amplify";
import { listMessages } from "./../graphql/queries";
import { onCreateMessage } from "./../graphql/subscriptions";
export default {
  name: "Messages",
  components: {},
  props: {
    userId: String,
  },
  data() {
    return {
      messages: [],
    };
  },
  async created() {
    const messagesRes = await API.graphql({
      query: listMessages,
    });
    this.messages = messagesRes.data.listMessages.items;
    API.graphql({ query: onCreateMessage }).subscribe({
      next: (eventData) => {
        const message = eventData.value.data.onCreateMessage;
        this.messages = [...this.messages, message];
      },
    });
  },
  methods: {
  },
};
</script>
