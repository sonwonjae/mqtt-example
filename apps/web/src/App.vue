<template>
  <h1>MQTT Example</h1>
  <br />
  <MqttControlTower />
  <br />
  <hr />
  <br />
  <h2>MQTT Clients</h2>
  <MqttClient
    v-for="client in CLIENTS"
    :key="client.id"
    :client-id="client.id"
  />
</template>

<script lang="ts">
import MqttControlTower from './components/MqttControlTower.vue';
import MqttClient from './components/MqttClient.vue';

import { CLIENTS } from './constants';

export default {
  name: 'MainApp',
  components: {
    MqttControlTower,
    MqttClient,
  },
  data() {
    return {
      CLIENTS,
    };
  }
};
</script>