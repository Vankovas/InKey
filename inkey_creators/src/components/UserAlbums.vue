<template>
  <div id="UserAlbums">
    <CreateEditAlbumModal
      v-if="createEditAlbumModal.visible"
      @hideModal="hideAlbumModal"
      :mode="createEditAlbumModal.mode"
      :album="createEditAlbumModal.album"
    ></CreateEditAlbumModal>
    <DeleteItemModal
      v-if="deleteItemModal.visible"
      @hideModal="hideDeleteItemModal"
      :type="deleteItemModal.type"
      :item="deleteItemModal.item"
    ></DeleteItemModal>
    <h1>Albums</h1>
    <p>Controls</p>
    <table>
      <tbody>
        <tr>
          <td>Create Album via Modal</td>
          <td>
            <button @click="showCreateModal">Execute</button>
          </td>
        </tr>
        <tr>
          <td>Test Create Albums</td>
          <td>
            <input type="file" @change="getImg" />
          </td>
          <td>
            <button @click="createTestAlbum">Execute</button>
          </td>
        </tr>
        <tr>
          <td>Fetch all albums</td>
          <td>
            <button @click="fetchAllAlbums">Execute</button>
          </td>
        </tr>
        <tr>
          <td>Fetch album by id</td>
          <td>
            <input type="number" v-model="id" />
          </td>
          <td>
            <button @click="fetchAlbum">Execute</button>
          </td>
        </tr>
        <tr>
          <td>Update album by id</td>
          <td>
            <input type="number" v-model="albumUpdateId" />
          </td>
          <td>
            <span>Name:</span>
            <input type="text" v-model="albumUpdateName" />
          </td>
          <td>
            <span>Is Private:</span>
            <br />
            <input type="checkbox" v-model="albumUpdateIsPrivate" />
            <span>{{ this.albumUpdateIsPrivate ? 'TRUE' : 'FALSE' }}</span>
          </td>
          <td>
            <button @click="updateAlbum">Execute</button>
          </td>
        </tr>
        <tr>
          <td>Delete album by id</td>
          <td>
            <input type="number" v-model="albumDeleteId" />
          </td>
          <td>
            <button @click="showDeleteAlbumModal">Execute</button>
          </td>
        </tr>
      </tbody>
    </table>

    <p>Selected Album</p>
    <table>
      <thead>
        <tr>
          <th>ID</th>
          <th>Cover Photo</th>
          <th>Name</th>
          <th>Description</th>
          <th>Is Private</th>
          <th>
            Edit
          </th>
        </tr>
      </thead>

      <tbody>
        <tr>
          <td>{{ selectedAlbum.id }}</td>
          <td>
            <img :src="selectedAlbum.cover_image" alt="Album Cover" />
          </td>
          <td>{{ selectedAlbum.name }}</td>
          <td>{{ selectedAlbum.description }}</td>
          <td>{{ selectedAlbum.is_private === true ? 'Yes' : 'No' }}</td>
          <td>
            <input type="submit" value="EDIT" @click="showEditModal" />
          </td>
        </tr>
      </tbody>
    </table>

    <p>All Albums</p>
    <table>
      <thead>
        <tr>
          <th>ID</th>
          <th>Cover Photo</th>
          <th>Name</th>
          <th>Description</th>
          <th>Is Private</th>
        </tr>
      </thead>

      <tbody>
        <tr v-for="album in allAlbums" :key="album.id">
          <td>{{ album.id }}</td>
          <td>
            <img :src="album.cover_image" alt="Album Cover" />
          </td>
          <td>{{ album.name }}</td>
          <td>{{ album.description }}</td>
          <td>{{ album.is_private === true ? 'Yes' : 'No' }}</td>
        </tr>
      </tbody>
    </table>
  </div>
</template>

<script>
import { mapGetters } from 'vuex';
import { CREATE_ALBUM, FETCH_ALL_ALBUMS, FETCH_ALBUM, UPDATE_ALBUM, DELETE_ALBUM } from '../store/actions.type';
import CreateEditAlbumModal from './CreateEditAlbumModal';
import DeleteItemModal from './DeleteItemModal';

export default {
  data() {
    return {
      id: 0,
      albumUpdateId: 0,
      albumUpdateName: '',
      albumUpdateIsPrivate: false,
      albumDeleteId: 0,
      img: null,
      createEditAlbumModal: {
        mode: '',
        visible: false,
        album: null,
      },
      deleteItemModal: {
        type: '',
        visible: false,
        item: null,
      },
    };
  },
  name: 'UserAlbums',
  components: {
    CreateEditAlbumModal,
    DeleteItemModal,
  },
  methods: {
    getImg(e) {
      this.img = e.target.files[0];
    },
    createTestAlbum() {
      const albumDetails = {
        description: 'na lora ipsuma',
        is_private: false,
        cover_image: this.img,
      };

      for (let i = 0; i < 10; i++) {
        albumDetails.name = `Test Album ${i}`;
        this.$store.dispatch(CREATE_ALBUM, albumDetails);
      }
    },
    fetchAllAlbums() {
      this.$store.dispatch(FETCH_ALL_ALBUMS, currentCreator.id);
    },
    fetchAlbum() {
      this.$store.dispatch(FETCH_ALBUM, this.id);
    },
    updateAlbum() {
      const updatedData = {
        id: this.albumUpdateId,
        description: '',
        name: this.albumUpdateName,
        is_private: this.albumUpdateIsPrivate,
      };
      this.$store.dispatch(UPDATE_ALBUM, updatedData);
    },
    deleteAlbum() {
      this.$store.dispatch(DELETE_ALBUM, this.albumDeleteId);
    },
    showEditModal() {
      this.createEditAlbumModal.mode = 'Edit';
      this.createEditAlbumModal.visible = true;
      this.createEditAlbumModal.album = this.selectedAlbum;
    },
    showCreateModal() {
      this.createEditAlbumModal.mode = 'Create';
      this.createEditAlbumModal.visible = true;
      this.createEditAlbumModal.album = {};
    },
    hideAlbumModal() {
      this.createEditAlbumModal.visible = false;
      this.createEditAlbumModal.album = {};
    },
    showDeleteAlbumModal() {
      this.$store.dispatch(FETCH_ALBUM, this.albumDeleteId);
      setTimeout(() => {
        this.deleteItemModal.type = 'album';
        this.deleteItemModal.visible = true;
        this.deleteItemModal.item = this.selectedAlbum;
      }, 1000);
    },
    hideDeleteItemModal() {
      this.deleteItemModal.visible = false;
      this.deleteItemModal.item = {};
    },
  },
  computed: {
    ...mapGetters(['allAlbums', 'selectedAlbum', 'currentCreator']),
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
