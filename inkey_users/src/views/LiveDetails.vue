<template>
  <div id="LiveDetails">
    <Chat ref="chat" :socket="this.socket"></Chat>
  </div>
</template>

<script>
import io from 'socket.io-client';
import { EventBus } from '../common/event-bus';
import { SOCKET_URL } from '../common/config';
import Chat from '../components/Chat';

export default {
  name: 'LiveDetails',

  components: {
    Chat,
  },

  beforeRouteEnter(to, from, next) {
    next((vm) => {
      vm.socket = io(SOCKET_URL);
      // here instead of "1" should be the uuid of the stream
      vm.socket.emit('enter', vm.id);
      vm.socket.on('stream', (arrayBuffer) => {
        let blob = new Uint8Array(arrayBuffer);
        vm.queue.push(blob);
        vm.appendToSourceBuffer();
      });
      vm.$refs['chat'].connectSocket(vm.socket);
      vm.mediaSource = new MediaSource();
      EventBus.$on('player-received', (player) => {
        let url = URL.createObjectURL(vm.mediaSource);
        vm.mediaSource.addEventListener('sourceopen', () => {
          vm.sourceBuffer = vm.mediaSource.addSourceBuffer('audio/webm;codecs=opus');
          vm.sourceBuffer.mode = 'sequence';
          vm.mediaSource.duration = 0;
          vm.sourceBuffer.addEventListener('updateend', () => {
            EventBus.$emit('set-duration', vm.player.duration);
          });
        });
        if (!vm.interval) {
          vm.interval = setInterval(() => {
            vm.appendToSourceBuffer();
          }, 1000);
        }
        vm.player = player;
        vm.player.src = url;
        vm.mediaSource;
      });
      EventBus.$emit('stream-start');
    });
  },

  data() {
    return {
      socket: undefined,
      id: this.$route.params.id,
      isListening: false,
      queue: [],
      sourceBuffer: null,
      mediaSource: null,
      player: null,
      interval: undefined,
    };
  },

  methods: {
    concat(arrays) {
      // sum of individual array lengths
      let totalLength = arrays.reduce((acc, value) => acc + value.length, 0);
      if (!arrays.length) return null;
      let result = new Uint8Array(totalLength);
      // for each array - copy it over result
      // next array is copied right after the previous one
      let length = 0;
      for (let array of arrays) {
        result.set(array, length);
        length += array.length;
      }

      return result;
    },

    appendToSourceBuffer() {
      if (this.mediaSource.readyState === 'open' && this.sourceBuffer && this.sourceBuffer.updating === false) {
        if (this.queue.length != 0) {
          let full = this.concat(this.queue);
          this.sourceBuffer.appendBuffer(full);
          this.queue = [];
        }
      }

      // Limit the total buffer size to 20 minutes
      // This way we don't run out of RAM
      if (this.player.buffered.length && this.player.buffered.end(0) - this.player.buffered.start(0) > 1200) {
        this.sourceBuffer.remove(0, this.player.buffered.end(0) - 1200);
      }
    },

    destroySocket() {
      EventBus.$emit('stream-end');
      clearInterval(this.interval);
      this.socket.emit('disconnect');
      this.socket.disconnect();
      this.$refs.chat.messages = [];
    },
  },

  beforeRouteLeave(to, from, next) {
    this.destroySocket();

    next();
  },

  beforeDestroy() {
    this.destroySocket();
  },
};
</script>

<style lang="scss" scoped></style>
