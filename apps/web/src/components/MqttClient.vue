<template>
  <div>
    <h3>{{ clientId }}</h3>
    <div v-for="(msg, i) in receivedMessages" :key="i">📩 {{ msg }}</div>
  </div>
</template>

<script>
import mqtt from 'mqtt';

export default {
  name: 'MqttClient',

  props: {
    clientId: {
      type: String,
      required: true,
    },
    topic: {
      type: String,
      default: 'test/topic',
    },
  },

  data() {
    return {
      isMoving: false,
      client: null,
      position: {
        x: 0,
        y: 0,
        z: 0,
      },
      receivedMessages: [],
    };
  },

  methods: {
    connect() {
      this.client = mqtt.connect('ws://localhost:9001', {
        clientId: this.clientId,
      });
    },
    subscribe() {
      this.client.on('connect', () => {
        console.log(`${this.clientId} connected`);
        this.client.subscribe(this.clientId);
      });
      this.client.on('message', this.move);
    },
    move(topic, position) {
      if (topic !== this.clientId) {
        return;
      }
      const [tx, ty, tz] = position.toString().split(':').map(Number);
      if (this.position.x === tx && this.position.y === ty && this.position.z === tz) {
        this.receivedMessages.push(`${this.clientId}: 이미 지정된 위치에 존재합니다.`);
        return;
      }
      if (this.isMoving) {
        this.receivedMessages.push(`${this.clientId}: 현재 이동 중입니다.`);
        return;
      }
      this.receivedMessages.push(`${this.clientId}: 이동 시작`);
      this.isMoving = true;
      setTimeout(() => {
        this.position.x = tx;
        this.position.y = ty;
        this.position.z = tz;
        this.receivedMessages.push(`${this.clientId}: 이동 완료`);
        this.client.publish('control-tower-logs', `${this.clientId}: 이동 완료`);
        this.isMoving = false;
      }, 500)
    }
  },

  mounted() {
    this.connect();
    this.subscribe();
  },

  beforeUnmount() {
    if (this.client) {
      this.client.end();
    }
  },
};
</script>

<style scoped>
div {
  margin-bottom: 1rem;
  padding: 1rem;
  border: 1px solid #ccc;
}
</style>
