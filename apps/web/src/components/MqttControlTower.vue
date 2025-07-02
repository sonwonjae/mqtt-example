<template>
  <div class="mqtt-control-tower">
    <h2>Mqtt Control Tower</h2>
    <div>current client id: {{ selectedClientId }}</div>
    <select v-model="selectedClientId">
        <option v-for="client in CLIENTS" :key="client.id" :value="client.id">
            {{ client.name }}
        </option>
    </select>
    <button @click="sendOrder">이동</button>
    <ul>
      <li>
        <label for="x" >x: </label>
        <input id="x" type="number" v-model="x" />
      </li>
      <li>
        <label for="y" >y: </label>
        <input type="number" v-model="y" />
      </li>
      <li>
        <label for="z" >z: </label>
        <input type="number" v-model="z" />
      </li>
    </ul>
    <br />
    <hr />
    <br />
    <h2>Mqtt logs</h2>
    <div>
      <ul>
        <li v-for="(log, index) in logs" :key="index">
          {{ log }}
        </li>
      </ul>
    </div>
  </div>
</template>
<script>
import mqtt from 'mqtt';
import { CLIENTS } from '../constants';

export default {
  name: 'MqttControlTower',
  data() {
    return {
      CLIENTS,
      selectedClientId: CLIENTS[0].id,
      x: 0,
      y: 0,
      z: 0,
      logs: [],
      mqttClient: mqtt.connect('ws://localhost:9001')
    };
  },
  methods: {
    sendOrder () {
      console.log('run')
      this.mqttClient.publish(`${this.selectedClientId}`, `${this.x}:${this.y}:${this.z}`)
    },
  },
  mounted() {
    this.mqttClient.subscribe('control-tower-logs');
    this.mqttClient.on('message', (topic, log) => {
      if (topic !== 'control-tower-logs') {
        return;
      }
      this.logs.push(log.toString());
    })
  }
};
</script>