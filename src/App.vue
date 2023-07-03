<template>
  <router-view/>
</template>

<script>
import emitter from './bus';

const { EventHubConsumerClient } = require('@azure/event-hubs');

export default {
  name: 'App',
  created() {
    this.receiveEventHubData();
  },
  methods: {
    receiveEventHubData() {
      const connectionString = process.env.VUE_APP_EVENTHUB_CONNECTION_STRING;
      const eventHubName = process.env.VUE_APP_EVENTHUB_NAME;
      const consumerGroup = process.env.VUE_APP_CONSUMER_GROUP_NAME;

      const consumerClient = new EventHubConsumerClient(
        consumerGroup,
        connectionString,
        eventHubName,
      );
      this.subscription = consumerClient.subscribe({
        processEvents: async (events, context) => {
          console.log(`events: ${JSON.stringify(events[0].body)}`);
          // this.$eventbus.$emit('telemetryEvent', events[0].body);
          emitter.emit('telemetryEvent', events[0].body);
          await context.updateCheckpoint(events[events.length - 1]);
        },
        // processError: async (err, context) => {
        //   console.log(`Error: ${err}, context: ${context}`);
        // },
      });
    },
  },
};
</script>

<style lang="scss">
#app {
  font-family: Avenir, Helvetica, Arial, sans-serif;
  -webkit-font-smoothing: antialiased;
  -moz-osx-font-smoothing: grayscale;
  text-align: center;
  color: #2c3e50;
}

nav {
  padding: 30px;

  a {
    font-weight: bold;
    color: #2c3e50;

    &.router-link-exact-active {
      color: #42b983;
    }
  }
}
</style>
