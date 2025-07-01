<template>
  <div>
    <h3>{{ clientId }}</h3>
    <input v-model="message" placeholder="메시지 입력" />
    <button @click="sendMessage">보내기</button>
    <div v-for="(msg, i) in receivedMessages" :key="i">📩 {{ msg }}</div>
  </div>
</template>

<script setup>
import { ref, onMounted, onUnmounted } from 'vue';
import mqtt from 'mqtt';

const props = defineProps({
  clientId: String,
  topic: {
    type: String,
    default: 'test/topic',
  },
});

const client = ref(null);
const message = ref('');
const receivedMessages = ref([]);

onMounted(() => {
  client.value = mqtt.connect('ws://localhost:9001', {
    clientId: props.clientId,
  });

  client.value.on('connect', () => {
    console.log(`${props.clientId} connected`);
    client.value.subscribe(props.topic);
  });

  client.value.on('message', (topic, payload) => {
    receivedMessages.value.push(`${topic}: ${payload.toString()}`);
  });
});

onUnmounted(() => {
  client.value?.end();
});

function sendMessage() {
  client.value.publish(props.topic, `${props.clientId}: ${message.value}`);
  message.value = '';
}
</script>

<style scoped>
div {
  margin-bottom: 1rem;
  padding: 1rem;
  border: 1px solid #ccc;
}
</style>
