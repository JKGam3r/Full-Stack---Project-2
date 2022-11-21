<template>
  <div id="page">
    <table class="table-format">
      <tr class="row-format">
        <th id="title-col">Title</th>
        <th id="desc-col">Description</th>
        <th id="deadline-col">Deadline</th>
        <th id="priority-col">Priority</th>
        <th id="iscomp-col">Is Complete</th>
        <th id="action-col">Action</th>
      </tr>
      <tr
        class="row-format"
        v-for="(message, index) in dataList"
        :item="message"
        :key="index"
      >
        <td>{{ message[2] }}</td>
        <td>{{ message[3] }}</td>
        <td>{{ message[4] }}</td>
        <td>{{ message[5] }}</td>
        <td>
          <input type="checkbox" @click="toggleUpdateBtnVis(message[1])" />
        </td>
        <td>
          <button
            type="button"
            class="btn btn-primary"
            v-if="isUpdateVisible(message[1])"
            @click="updateRow(message[1])"
          >
            <i class="fas fa-edit"></i>
            UPDATE
          </button>
          <br />
          <button
            type="button"
            class="btn btn-danger"
            @click="deleteRow(message[1])"
          >
            <i class="fa fa-times-circle"></i>
            DELETE
          </button>
        </td>
      </tr>
    </table>
  </div>
</template>

<script>
import 'bootstrap/dist/css/bootstrap.min.css';

import toastr from 'toastr';
import 'toastr/build/toastr.css';

export default {
  name: 'Page',

  components: {},

  data() {
    return {
      dataList: [],
      upBtnVis: [],
    };
  },

  props: {
    rowData: {
      type: Array,
    },
  },

  methods: {
    showModal() {
      this.isModalVisible = true;
    },
    toggleUpdateBtnVis(id) {
      for (let i = 0; i < this.dataList.length; i++) {
        if (this.dataList[i][1] == id) {
          this.upBtnVis[i] = !this.upBtnVis[i];
        }
      }
    },
    isUpdateVisible(id) {
      for (let i = 0; i < this.dataList.length; i++) {
        if (this.dataList[i][1] == id) {
          return this.upBtnVis[i];
        }
      }
      return true;
    },
    updateRow(id) {
      this.$emit('openUpdate', id);
    },
    deleteRow(id) {
      for (let i = 0; i < this.dataList.length; i++) {
        if (this.dataList[i][1] == id) {
          this.dataList.splice(i, 1);
          this.upBtnVis.splice(i, 1);
          this.$emit('allUpdatedData', this.dataList);
          toastr['success']('Entry Successfully Deleted', '', {
            closeButton: true,
          });
        }
      }
    },
  },

  watch: {
    rowData: function () {
      if (this.rowData[0]) {
        this.dataList.push(this.rowData);
        this.upBtnVis.push(true);
        this.$emit('allUpdatedData', this.dataList);
      } else {
        for (let i = 0; i < this.dataList.length; i++) {
          if (this.dataList[i][1] == this.rowData[1]) {
            this.rowData[2] = this.dataList[i][2];
            this.dataList[i] = this.rowData;
          }
        }
      }
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

.table-format {
  position: absolute;
  height: 15%;
  width: 100%;
}

.row-format {
  border-bottom: 1px solid lightgray;
  padding: 15px 20px 10px 20px;
  font-size: 12px;
}
</style>
