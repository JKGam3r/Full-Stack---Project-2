<template>
  <div id="app">
    <!-- The header -->
    <div style="position: absolute; top: 0; bottom: 0; left: 0; right: 0">
      <h1 class="bg-primary text-white header-format">
        <i class="fa fa-solid fa-bars"></i>
        FRAMEWORKS
      </h1>
      <form>
        <!-- Display the dialog -->
        <button type="button" class="add-button-format" @click="showModal">
          <i class="fa fa fa-plus-circle"></i>
          ADD
        </button>
      </form>
    </div>
    <Dialog
      @inputData="addRow"
      @close="closeModal"
      v-show="isModalVisible"
      :isAdd="isAddDialog"
      :updateId="id"
      :allRows="allData"
    />
    <Page
      :rowData="childData"
      @openUpdate="showUpdateModal"
      @allUpdatedData="setUpdatedData"
    />
  </div>
</template>

<script>
import Page from './components/Page.vue';
import Dialog from './components/Dialog.vue';
import 'bootstrap/dist/css/bootstrap.min.css';

import { createApp } from 'vue';

export default {
  name: 'App',
  components: {
    Page,
    Dialog,
  },

  data() {
    return {
      isModalVisible: false,
      childData: [],
      isAddDialog: true,
      id: 0,
      allData: [],
    };
  },

  methods: {
    showModal() {
      this.isModalVisible = true;
      this.isAddDialog = true;
    },
    closeModal() {
      this.isModalVisible = false;
      this.isAddDialog = true;
    },
    addRow(variable) {
      this.childData = variable;
    },
    showUpdateModal(my_id) {
      this.isModalVisible = true;
      this.isAddDialog = false;
      this.id = my_id;
    },
    setUpdatedData(newUpdate) {
      this.allData = newUpdate;
    },
  },
};
</script>

<style>
#app {
  font-family: Avenir, Helvetica, Arial, sans-serif;
  -webkit-font-smoothing: antialiased;
  -moz-osx-font-smoothing: grayscale;
  text-align: center;
  color: #2c3e50;
  margin-top: 60px;
}

.header-format {
  padding: 15px;
}

.add-button-format {
  background-color: rgb(0, 125, 225);
  color: white;
  border-radius: 5px;
  border-color: rgb(0, 100, 200);
  padding: 3px 23px 3px 23px;
  margin: -60px 0px 0px 0px;
  float: right;
}
</style>
