<script>
import MqttClient from './components/MqttClient.vue';

export default {
  name: 'MqttExample',
  components: {
    MqttClient
  },
  data() {
    return {
      clientIds: ['client-1', 'client-2', 'client-3']
    };
  }
};
</script>

<template>
  <h1>MQTT Example</h1>
  <h2>MQTT Broker</h2>
  <div>broker</div>
  <h2>MQTT Clients</h2>
  <MqttClient v-for="id in clientIds" :key="id" :client-id="id" />
</template>