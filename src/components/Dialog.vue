<template>
  <!-- I used the following websites to help me with the template of my dialog, and then drastically modified it to fit the needs of the project. -->
  <!-- https://www.digitalocean.com/community/tutorials/vuejs-vue-modal-component -->
  <!-- https://codesandbox.io/s/keen-hooks-lngreg?file=/style.css -->
  <div id="dialog">
    <div class="modal-backdrop">
      <div class="modal">
        <!-- The heading for the dialog -->
        <header class="modal-header bg-primary">
          <slot v-if="isAdd" name="header">
            <i class="fa fa-plus-circle"> Add Task</i>
          </slot>
          <slot v-else name="header">
            <i class="fas fa-edit"> Edit Task</i>
          </slot>
        </header>

        <!-- The place where the user inputs data -->
        <section class="modal-body">
          <slot name="body">
            <form>
              <input
                id="title-txt"
                t
                v-model="title_text"
                v-if="isAdd"
                :class="{
                  'dialog-textbox-warning-format':
                    highlight_title_txtbx || titleUsed,
                }"
                :class="{
                  'dialog-textbox-format': !highlight_title_txtbx && !titleUsed,
                }"
                type="text"
                placeholder="Title"
              />
              <p
                class="dialog-warning-text-format"
                v-if="highlight_title_txtbx && isAdd"
              >
                Title is Required!
              </p>
              <p
                class="dialog-warning-text-format"
                v-else-if="titleUsed && isAdd"
              >
                Title is Already In Use!
              </p>
              <input
                id="desc-txt"
                v-model="desc_text"
                :class="{
                  'dialog-textbox-warning-format': highlight_desc_txtbx,
                }"
                :class="{ 'dialog-textbox-format': !highlight_desc_txtbx }"
                type="text"
                placeholder="Description"
              />
              <p class="dialog-warning-text-format" v-if="highlight_desc_txtbx">
                Description is Required!
              </p>
              <input
                type="date"
                v-model="date_input"
                class="dialog-datebox-format"
                id="deadline"
                name="deadline"
              />
              <div>
                <p class="priority-text-format">Priority</p>
                <br />
                <div>
                  <label for="Low"
                    ><input
                      class="dialog-radio-button-format"
                      id="Low"
                      value="Low"
                      type="radio"
                      name="priority"
                      v-model="priority_type"
                    />
                    Low</label
                  >
                  <label for="Med"
                    ><input
                      class="dialog-radio-button-format"
                      id="Med"
                      value="Med"
                      type="radio"
                      name="priority"
                      v-model="priority_type"
                    />
                    Med</label
                  >
                  <label for="High"
                    ><input
                      class="dialog-radio-button-format"
                      id="High"
                      value="High"
                      type="radio"
                      name="priority"
                      v-model="priority_type"
                    />
                    High</label
                  >
                </div>
              </div>
            </form>
          </slot>
        </section>

        <!-- The footer for the dialog box -->
        <footer class="modal-footer">
          <slot name="footer">
            <form>
              <button
                type="button"
                class="btn btn-danger dialog-button-format"
                @click="close"
              >
                <i class="fa fa-ban"></i>
                CANCEL
              </button>
              <button
                type="button"
                class="btn btn-primary dialog-button-format"
                @click="verify"
              >
                <div v-if="isAdd">
                  <i class="fa fa-plus-circle"></i>
                  ADD
                </div>
                <div v-else>
                  <i class="fas fa-edit"></i>
                  EDIT
                </div>
              </button>
            </form>
          </slot>
        </footer>
      </div>
    </div>
  </div>
</template>

<script>
import toastr from 'toastr';
import 'toastr/build/toastr.css';
import moment from 'moment';

export default {
  name: 'Dialog',

  components: {},

  props: {
    isAdd: {
      type: Boolean,
    },
    updateId: {
      type: Number,
    },
    allRows: {
      type: Array,
    },
  },

  data() {
    return {
      title_text: '',
      desc_text: '',
      date_input: '',
      priority_type: '',
      current_id: 0,

      highlight_title_txtbx: false,
      highlight_desc_txtbx: false,
      titleUsed: false,
    };
  },

  methods: {
    // Close dialog
    close() {
      this.title_text = '';
      this.desc_text = '';
      this.date_input = '';
      this.priority_type = '';
      this.highlight_title_txtbx = false;
      this.highlight_desc_txtbx = false;
      this.titleUsed = false;
      this.$emit('close');
    },
    // Make sure all fields are filled in
    verify() {
      // If some field should be highlighted for not being complete
      this.highlight_title_txtbx = this.title_text == '';
      this.highlight_desc_txtbx = this.desc_text == '';

      // Check the name first to see if it's unique
      for (let i = 0; i < this.allRows.length; i++) {
        if (this.allRows[i][2] == this.title_text) {
          this.titleUsed = true;
        }
      }

      // Validate the different fields
      if (
        (this.title_text != '' || !this.isAdd) &&
        this.desc_text != '' &&
        this.date_input != '' &&
        this.priority_type != ''
      ) {
        if (this.isAdd) {
          this.titleUsed = false;
          if (!this.titleUsed) {
            this.$emit('inputData', [
              this.isAdd,
              this.current_id,
              this.title_text,
              this.desc_text,
              moment(new Date(this.date_input)).format('MM/DD/YYYY'),
              this.priority_type,
            ]);
            this.current_id++;
            this.$emit('close');
            this.title_text = '';
            this.desc_text = '';
            this.date_input = '';
            this.priority_type = '';
            this.highlight_title_txtbx = false;
            this.highlight_desc_txtbx = false;
            this.titleUsed = false;

            toastr['success']('Entry Successfully Added', '', {
              closeButton: true,
            });
          }
        } else {
          this.$emit('inputData', [
            this.isAdd,
            this.updateId,
            '',
            this.desc_text,
            moment(new Date(this.date_input)).format('MM/DD/YYYY'),
            this.priority_type,
          ]);
          this.$emit('close');
          this.title_text = '';
          this.desc_text = '';
          this.date_input = '';
          this.priority_type = '';
          this.highlight_title_txtbx = false;
          this.highlight_desc_txtbx = false;
          this.titleUsed = false;

          toastr['success']('Entry Successfully Updated', '', {
            closeButton: true,
          });
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

.modal-backdrop {
  position: fixed;
  top: 0;
  bottom: 0;
  left: 0;
  right: 0;
  background-color: rgba(0, 0, 0, 0.3);
  display: flex;
  justify-content: center;
  align-items: center;
}

.modal {
  box-shadow: 2px 2px 20px 1px;
  display: flex;
  flex-direction: column;
  width: 250px;
  height: 400px;
  padding: 0px;
  background-color: #fff;
  border-radius: 3px;
  position: relative;
}

.modal-header {
  padding: 15px;
  display: flex;
  border-radius: 0px 0px 3px 0px;
  color: white;
}

.dialog-header-format {
  color: white;
  float: left;
  padding: 15px;
}

.dialog-textbox-format {
  padding: 10px;
  margin: 15px 5px 15px 5px;
}

.dialog-textbox-format::placeholder {
  color: gray;
}

.dialog-textbox-format:focus {
  outline: none;
  border: 3px solid black;
  box-shadow: 0 0 10px #719ece;
}

.dialog-textbox-warning-format {
  padding: 10px;
  margin: 15px 5px 15px 5px;
  border-color: red;
  border-width: 1px;
  border-radius: 3px;
}

.dialog-textbox-warning-format::placeholder {
  color: red;
}

.dialog-textbox-warning-format:focus {
  outline: none;
  border: 3px solid red;
  box-shadow: 0 0 10px #719ece;
}

.dialog-warning-text-format {
  color: red;
  padding: -8px;
  margin: -8px;
  font-size: 10px;
}

.dialog-datebox-format {
  padding: 10px;
  margin: 15px 5px 15px 5px;
  width: 85%;
}

.priority-text-format {
  padding: 0px 0px 0px 35px;
  margin: 0px;
  float: left;
}

.dialog-button-format {
  padding: 3px 15px 3px 15px;
  margin: 3px;
  float: right;
  width: 100px;
  font-size: 12px;
  border-radius: 3px;
}

.dialog-radio-button-format {
  margin: 0px 7px 0 7px;
}

.modal-footer {
  border-top: 1px solid #eeeeee;
  flex-direction: column;
  justify-content: flex-end;
  padding: 15px 0px 0px 0px;
  display: flex;
  border-radius: 0px 0px 3px 0px;
  color: white;
}

.modal-body {
  position: relative;
  padding: 0px;
}

.btn-close {
  position: absolute;
  top: 0;
  right: 0;
  border: none;
  font-size: 20px;
  padding: 10px;
  cursor: pointer;
  font-weight: bold;
  color: #4aae9b;
  background: transparent;
}

.btn-green {
  color: white;
  background: #4aae9b;
  border: 1px solid #4aae9b;
  border-radius: 2px;
}
</style>
