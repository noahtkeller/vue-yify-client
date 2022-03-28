<template>
  <v-card v-show="fileName" class="movie-container">
    <v-card-title>{{fileName}}</v-card-title>
    <v-card-text v-if="showDetails">
      <div>{{downloaded}} of {{size}}</div>
      <div>Down Speed: {{downSpeed}}</div>
      <div>Up Speed: {{upSpeed}}</div>
      <div>Progress: {{progress}}</div>
      <div>Remaining {{timeRemaining}}</div>
      <div>Ratio: {{ratio}}</div>
      <div>Peers: {{numPeers}}</div>
    </v-card-text>
    <div id="output"></div>
  </v-card>
</template>

<script>
import WebTorrent from 'webtorrent';

const trackers = [
  'wss://tracker.openwebtorrent.com',
  'wss://tracker.files.fm:7073/announce',
  'wss://spacetradersapi-chatbox.herokuapp.com:443/announce',
  'wss://qot.abiir.top:443/announce',
  'ws://tracker.files.fm:7072/announce',
  'ws://localhost:8990',
];

export default {
  name: 'MovieClient',
  data() {
    return { showDetails: false, fileName: null, timeRemaining: null, ratio: null, numPeers: null, download: null, downloaded: null, size: null, downSpeed: null, upSpeed: null, progress: null };
  },
  methods: {
    getMagnet(infoHash) {
      const tracker = trackers.map((t) => `tr=${encodeURIComponent(t)}`).join('&');
      const xs = `xs=http://localhost:8990/torrent/${infoHash}`;
      return `magnet:?xt=urn:btih:${infoHash}&dn=${infoHash}&${tracker}&${xs}`;
    },
    startTorrent(infoHash) {
      const magnet = this.getMagnet(infoHash);
      this.client.add(magnet, (torrent) => {
        const [largestFile] = torrent.files.sort((a, b) => b.length - a.length);
        this.size = largestFile.length;
        this.fileName = largestFile.name;
        this.downloaded = 0;
        largestFile.appendTo('#output');
        const el = document.querySelector('#output video');
        el.style.width = '100%';
        torrent.on('download', (bytes) => {
          this.downloaded += bytes;
          this.downSpeed = torrent.downloadSpeed;
          this.upSpeed = torrent.uploadSpeed;
          this.progress = torrent.progress;
          this.timeRemaining = torrent.timeRemaining;
          this.numPeers = torrent.numPeers;
          this.ratio = torrent.ratio;
        });
      });
    },
  },
  props: ['infoHash', 'modelValue'],
  watch: {
    infoHash() {
      this.startTorrent(this.infoHash);
    },
    fileName() {
      this.$emit('update:modelValue', this.fileName);
    },
  },
  mounted() {
    this.client = new WebTorrent();
    this.client.on('error', (err) => {
      console.error('ERROR: ' + err.message);
    });
    this.client.on('peer', (addr) => {
      console.log(' ');
      console.log('found a peer: ' + addr) // 85.10.239.191:48623
    });
    if (this.infoHash) {
      this.startTorrent(this.infoHash);
    }
  },
  async unmounted() {
    if (this.client) {
      await new Promise((res, rej) => this.client.destroy((e, r) => e ? rej(e) : res(r)));
    }
  }
}
</script>

<style scoped>

</style>
