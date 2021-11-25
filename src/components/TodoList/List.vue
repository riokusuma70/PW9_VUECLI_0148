<template>
  <v-main class="list">
    <h3 class="text-h3 font-weight-medium mb-5">To Do List</h3>
    <v-card>
      <v-card-title>
        <v-text-field v-model="search" append-icon="mdi-magnify" label="Search" single-line hide-details></v-text-field>
        <v-spacer></v-spacer>
        <v-btn color="success" dark @click="dialog = true"> Tambah </v-btn>
      </v-card-title>
      <v-data-table :headers="headers" :items="todos" :search="search" :single-expand="singleExpand" :expanded.sync="expanded" item-key="note" show-expand>
        <template v-slot:[`item.actions`]="{ item }">
          <v-icon color="red" small class="mr-2" @click="editItem(item)"> mdi-pencil </v-icon>
          <v-icon color="green" small @click="deleteItem(item)"> mdi-delete </v-icon>
        </template>
        <template v-slot:[`item.priority`]="{ item }">
          <v-btn color="blue darken-1" text @click="showpriority(item)">Priority</v-btn>
        </template>
        <template v-slot:expanded-item="{ headers, item }">
          <td :colspan="headers.length">{{ item.note }}</td>
        </template>
      </v-data-table>
    </v-card>
    <v-dialog v-model="dialog" persistent max-width="600px">
      <v-card>
        <v-card-title>
          <span class="headline">Form Todo</span>
        </v-card-title>
        <v-card-text>
          <v-container>
            <v-text-field v-model="formTodo.task" label="Task" required></v-text-field>
            <v-select v-model="formTodo.priority" :items="['Penting', 'Biasa', 'Tidak Penting']" label="Priority" required></v-select>

            <v-textarea v-model="formTodo.note" label="Note" required></v-textarea>
          </v-container>
        </v-card-text>
        <v-card-actions>
          <v-spacer></v-spacer>
          <v-btn color="blue darken-1" text @click="cancel"> Cancel </v-btn>
          <v-btn color="blue darken-1" text @click="save"> Save </v-btn>
        </v-card-actions>
      </v-card>
    </v-dialog>
    <v-dialog v-model="dialogDelete" max-width="500px">
      <v-card>
        <v-card-title class="text-h5">Are you sure you want to delete this item?</v-card-title>
        <v-card-actions>
          <v-spacer></v-spacer>
          <v-btn color="blue darken-1" text @click="closeDelete">Cancel</v-btn>
          <v-btn color="blue darken-1" text @click="deleteItemConfirm">OK</v-btn>
          <v-spacer></v-spacer>
        </v-card-actions>
      </v-card>
    </v-dialog>
    <v-snackbar :timeout="1000" :value="true" v-model="penting" absolute top right color="red accent-4" elevation="24"> Penting </v-snackbar>
    <v-snackbar :timeout="1000" :value="true" v-model="tidakPenting" absolute bottom middle color="blue accent-4" elevation="24"> Tidak Penting </v-snackbar>
    <v-snackbar :timeout="1000" :value="true" v-model="biasa" absolute top left color="green accent-4" elevation="24"> Biasa </v-snackbar>
  </v-main>
</template>
<script>
export default {
  name: "List",
  data() {
    return {
      search: null,
      dialog: false,
      expanded: [],
      singleExpand: false,
      penting: false,
      tidakPenting: false,
      biasa: false,
      dialogDelete: false,
      headers: [
        {
          text: "Task",
          align: "start",
          sortable: true,
          value: "task",
        },
        { text: "Priority", value: "priority" },
        { text: "Note", value: "note" },
        { text: "Actions", value: "actions" },
      ],
      todos: [
        {
          task: "Coding",
          priority: "Penting",
          note: "Code for your life",
        },
        {
          task: "Gaming",
          priority: "Tidak Penting",
          note: "Wasting time",
        },
        {
          task: "Masak",
          priority: "Biasa",
          note: "Indomi saat coding terbaik gan",
        },
      ],
      formTodo: { task: null, priority: null, note: null },
    };
  },
  methods: {
    save() {
      if (this.editedIndex > -1) {
        Object.assign(this.todos[this.editedIndex], this.formTodo);
      } else {
        this.todos.push(this.formTodo);
      }
      this.resetForm();
      this.dialog = false;
    },
    cancel() {
      this.resetForm();
      this.dialog = false;
    },
    resetForm() {
      this.formTodo = { task: null, priority: null, note: null };
    },
    editItem(item) {
      this.editedIndex = this.todos.indexOf(item);
      this.formTodo = Object.assign({}, item);
      this.dialog = true;
    },
    deleteItem(item) {
      this.editedIndex = this.todos.indexOf(item);
      this.formTodo = Object.assign({}, item);
      this.dialogDelete = true;
    },
    closeDelete() {
      this.dialogDelete = false;
      this.$nextTick(() => {
        this.editedIndex = -1;
      });
    },
    deleteItemConfirm() {
      this.todos.splice(this.editedIndex, 1);
      this.closeDelete();
    },
    showpriority(item) {
      if (item.priority == "Penting") {
        this.penting = true;
      } else if (item.priority == "Tidak Penting") {
        this.tidakPenting = true;
      } else if (item.priority == "Biasa") {
        this.biasa = true;
      }
    },
  },
};
</script>
