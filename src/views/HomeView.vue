<template>
  <div class="home">
    <h3 class="m-5 "><b>Real-time Data from Edge & Azure IoT Hub Built-in endpoints</b></h3>
    <div class="table-data">
      <!-- {{ receiveData }} -->
      <table class="table">
        <thead class="thead-dark">
          <tr>
            <th class="text-center" scope="col">DeviceID</th>
            <th class="text-center" scope="col">Latitude</th>
            <th class="text-center" scope="col">Longitude</th>
            <th class="text-center" scope="col">Telemetry</th>
            <th class="text-center" scope="col">DateTime</th>
          </tr>
        </thead>
        <tbody>
          <tr class="hover-table">
            <td class="position-relative text-center pt-3">
              {{ receiveData.id }}
            </td>
            <td class="position-relative text-center pt-3">
              {{ receiveData.latitude }}
            </td>
            <td class="position-relative text-center pt-3">
              {{ receiveData.longitude }}
            </td>
            <td class="position-relative text-center pt-3">
              {{ receiveData.telemetry }}
            </td>
            <td class="position-relative text-center pt-3">
              {{ receiveData.timestamp }}
            </td>
          </tr>
        </tbody>
      </table>
    </div>
  </div>
</template>

<script>
import * as signalR from '@microsoft/signalr';
import emitter from '../bus';

export default {
  name: 'HomeView',
  data() {
    return {
      receiveData: {},
    };
  },
  components: {
  },
  mounted() {
    emitter.on('telemetryEvent', (payload) => {
      this.receiveData = payload;
      console.log(`telemetryEvent: ${JSON.stringify(this.receiveData)}`);
    });
    this.receiveSignalR();
  },
  methods: {
    async receiveSignalR() {
      // ./ngrok http 8080
      const apiBaseUrl = 'https://streaming-sample.azurewebsites.net';
      const connection = new signalR.HubConnectionBuilder()
        .withUrl(`${apiBaseUrl}/api`)
        .configureLogging(signalR.LogLevel.Information)
        .build();
      connection.on('newMessage', (message) => {
        console.log(`message: ${JSON.stringify(message)}`);
      });
      connection.start()
        .catch(console.error);
    },
  },
  // beforeUnmount() {
  //   bus.all.clear();
  // },
};
</script>
