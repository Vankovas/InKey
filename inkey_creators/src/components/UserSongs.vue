<template>
  <div id="UserSongs">
    <DeleteItemModal
      v-if="deleteItemModal.visible"
      @hideModal="hideDeleteItemModal"
      :type="deleteItemModal.type"
      :item="deleteItemModal.item"
    ></DeleteItemModal>
    <h1>Songs</h1>
    <p>Controls</p>
    <table>
      <tbody>
        <tr>
          <td>Create Test Songs</td>
          <td>
            <input type="file" @change="getSong" />
          </td>
          <td>
            <button @click="createTestSongs">Execute</button>
          </td>
        </tr>
        <tr>
          <td>Fetch All Creator Songs</td>
          <td>
            <button @click="fetchAllSongs">Execute</button>
          </td>
        </tr>
        <tr>
          <td>Fetch Song By SongID</td>
          <td>
            <input type="number" v-model="fetchSongId" />
          </td>
          <td>
            <button @click="fetchSongById">Execute</button>
          </td>
        </tr>
        <tr>
          <td>Update Song by SongID</td>
          <td>
            <input type="number" v-model="updateSongId" placeholder="id" />
          </td>
          <td>
            <input type="file" @change="getUpdateSongData" />
          </td>
          <td>
            <button @click="updateSongById">Execute</button>
          </td>
        </tr>
        <tr>
          <td>Delete Song By SongID</td>
          <td>
            <input type="number" v-model="deleteSongId" />
          </td>
          <td>
            <button @click="showDeleteSongModal">Execute</button>
          </td>
        </tr>
        <tr />
      </tbody>
    </table>

    <table>
      <thead>
        <tr>
          <th>Play</th>
          <th>ID</th>
          <th>Name</th>
          <th>Description</th>
          <th>Album</th>
        </tr>
      </thead>
      <tbody>
        <tr v-for="song in allSongs" :key="song.id">
          <td>
            <a :href="song.data" target="_blank">Play</a>
          </td>
          <td>{{ song.id }}</td>
          <td>{{ song.name }}</td>
          <td>{{ song.description }}</td>
          <td>{{ song.album }}</td>
        </tr>
      </tbody>
    </table>
  </div>
</template>

<script>
import { mapGetters } from 'vuex';
import { FETCH_SONG, FETCH_ALL_SONGS, CREATE_SONG, DELETE_SONG, UPDATE_SONG } from '../store/actions.type';
import DeleteItemModal from './DeleteItemModal';

export default {
  data() {
    return {
      fetchSongId: 0,
      deleteSongId: 0,
      updateSongId: 0,
      updateSongData: null,
      song: null,
      deleteItemModal: {
        type: '',
        visible: false,
        item: null,
      },
      i: 0,
      limit: 0,
    };
  },
  name: 'UserSongs',

  components: {
    DeleteItemModal,
  },

  methods: {
    getSong(e) {
      this.song = e.target.files[0];
    },
    getUpdateSongData(e) {
      this.updateSongData = e.target.files[0];
    },
    fetchSongById() {
      this.$store.dispatch(FETCH_SONG, this.fetchSongId);
    },
    fetchAllSongs() {
      this.$store.dispatch(FETCH_ALL_SONGS);
    },
    createTestSongs() {
      const songs = [
        {
          name: 'Screening Evaluation (Skit)',
          description: '1st Song',
          rating: 5,
          album: 24,
          data: this.song,
        },
        {
          name: 'I Lied (Intro)',
          description: '2nd Song',
          rating: 5,
          album: 24,
          data: this.song,
        },
        {
          name: 'Isis',
          description: '3rd Song',
          rating: 5,
          album: 24,
          data: this.song,
        },
        {
          name: 'The War',
          description: '4th Song',
          rating: 5,
          album: 24,
          data: this.song,
        },
        {
          name: 'Chris (Skit)',
          description: '5th Song',
          rating: 5,
          album: 24,
          data: this.song,
        },
        {
          name: 'I Love',
          description: '6th Song',
          rating: 5,
          album: 24,
          data: this.song,
        },
        {
          name: "Devil's Work",
          description: '7th Song',
          rating: 5,
          album: 24,
          data: this.song,
        },
        {
          name: 'Lotto',
          description: '8th Song',
          rating: 5,
          album: 24,
          data: this.song,
        },
        {
          name: 'Kevin (Skit)',
          description: '9th Song',
          rating: 5,
          album: 24,
          data: this.song,
        },
        {
          name: 'Gold Mine',
          description: '10th Song',
          rating: 5,
          album: 24,
          data: this.song,
        },
        {
          name: 'Finally',
          description: '11th Song',
          rating: 5,
          album: 24,
          data: this.song,
        },
        {
          name: '10 Bands',
          description: '12th Song',
          rating: 5,
          album: 24,
          data: this.song,
        },
        {
          name: 'Revenge',
          description: '13th Song',
          rating: 5,
          album: 24,
          data: this.song,
        },
        {
          name: "Comprehensive Evaluation (Skit)",
          description: '14th Song',
          rating: 5,
          album: 24,
          data: this.song,
        },
        {
          name: 'ADHD',
          description: '15th Song',
          rating: 5,
          album: 24,
          data: this.song,
        },
        {
          name: 'Still Cant Love',
          description: '16th Song',
          rating: 5,
          album: 24,
          data: this.song,
        },
        {
          name: 'Will',
          description: '17th Song',
          rating: 5,
          album: 24,
          data: this.song,
        },
        {
          name: 'Broke and Stupid',
          description: '18th Song',
          rating: 5,
          album: 24,
          data: this.song,
        },
      ];

      this.i = 0;
      this.limit = songs.length;

      console.log(songs);
      this.delayedAddition(songs);
    },
    delayedAddition(songs) {
      setTimeout(() => {
        //Add
        this.$store.dispatch(CREATE_SONG, songs[this.i]);
        //Increment Loop
        this.i++;
        //Conditional next with delay
        if (this.i < this.limit) this.delayedAddition(songs);
      }, 10000);
    },
    updateSongById() {
      const updatedSongData = {
        id: this.updateSongId,
        data: this.updateSongData,
      };

      this.$store.dispatch(UPDATE_SONG, updatedSongData);
    },
    deleteSongById() {
      this.$store.dispatch(DELETE_SONG, this.deleteSongId);
    },
    showDeleteSongModal() {
      this.$store.dispatch(FETCH_SONG, this.deleteSongId);
      setTimeout(() => {
        this.deleteItemModal.type = 'song';
        this.deleteItemModal.visible = true;
        this.deleteItemModal.item = this.selectedSong;
      }, 1000);
    },
    hideDeleteItemModal() {
      this.deleteItemModal.visible = false;
      this.deleteItemModal.item = {};
    },
  },
  computed: {
    ...mapGetters(['allSongs', 'selectedSong']),
  },
};
</script>

<style lang="scss" scoped>
h1 {
  text-align: center;
}

p {
  text-align: center;
  font-size: 2rem;
}

table {
  margin: 0 auto 2rem auto;
  border-spacing: 0.5px;

  td,
  th {
    border: 1px solid black;
    padding: 1rem;
  }
}

img {
  width: 3.2rem;
  height: 3.2rem;
}
</style>
