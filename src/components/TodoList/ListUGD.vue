<template>
  <v-main class="list">
    <h3 class="text-h3 font-weight-medium mb-5">To Do List</h3>
    <v-card>
      <v-card-title>
        <v-text-field
          v-model="search"
          append-icon="mdi-magnify"
          label="Search"
          single-line
          hide-details
        ></v-text-field>
        <v-spacer></v-spacer>

        <v-select
            v-model="sorting"
            flat
            hide-details
            @change="sortBy"
            :items="['Penting', 'Tidak penting']"
            label="Sort By"
            required
            dense
            outlined
        ></v-select>

        <v-spacer></v-spacer>       
        <v-btn color="success" dark @click="dialog = true"> Tambah </v-btn>
      </v-card-title>
      <v-data-table :headers="headers" :items="todos" :search="search" :single-expand="singleExpand" :expanded.sync="expanded" item-key="task" show-expand>
          <v-switch
            v-model="singleExpand"
            label="Single expand"
            class="mt-2"
          ></v-switch>
         <template v-slot:[`item.priority`]="{ item }">
                <td>
                    <v-chip small label outlined :color="setColor(item.priority)">{{ item.priority }}</v-chip>
                </td>
          </template>

          <template v-slot:expanded-item="{ headers, item }">
                <td :colspan="headers.length">
                    <b>Notes </b><br>{{ item.note }}
                </td>
        </template>
        <template v-slot:[`item.actions`]="{ item }">
            <v-icon small class="mr-2" @click="update(item)" icon color="blue">mdi-pencil</v-icon>
            <v-icon small class="mr-2" @click="deleteUp(item)" icon color="red" slot="activator">mdi-delete</v-icon>
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
            <v-text-field
              v-model="formTodo.task"
              label="Task"
              required
            ></v-text-field>
            <v-select
              v-model="formTodo.priority"
              :items="['Penting', 'Biasa', 'Tidak penting']"
              label="Priority"
              required
            ></v-select>
            <v-textarea
              v-model="formTodo.note"
              label="Note"
              required
            ></v-textarea>
          </v-container>
        </v-card-text>
        <v-card-actions>
          <v-spacer></v-spacer>
          <v-btn color="blue darken-1" text @click="cancel"> Cancel </v-btn>
          <v-btn color="blue darken-1" text @click="save"> Save </v-btn>
        </v-card-actions>
      </v-card>
    </v-dialog>

    <v-dialog v-model="dialogUpdate" persistent max-width="600px">
      <v-card>
        <v-card-title>
          <span class="headline">Form Todo</span>
        </v-card-title>
        <v-card-text>
          <v-container>
            <v-text-field
              v-model="formTodo.task"
              label="Task"
              required
            ></v-text-field>
            <v-select
              v-model="formTodo.priority"
              :items="['Penting', 'Biasa', 'Tidak penting']"
              label="Priority"
              required
            ></v-select>
            <v-textarea
              v-model="formTodo.note"
              label="Note"
              required
            ></v-textarea>
          </v-container>
        </v-card-text>
        <v-card-actions>
          <v-spacer></v-spacer>
          <v-btn color="blue darken-1" text @click="cancel"> Cancel </v-btn>
          <v-btn color="blue darken-1" text @click="updateUp"> Update </v-btn>
        </v-card-actions>
      </v-card>
    </v-dialog>

    <v-dialog v-model="dialogDelete" persistent max-width="350px">
      <v-card>
        <v-card-title>
          <p>Yakin ingin menghapus?</p>
        </v-card-title>
        <v-card-actions>
          <v-spacer></v-spacer>
          <v-btn color="green" text @click="cancel"> Tidak </v-btn>
          <v-btn color="red" text @click="deleteItem"> Ya </v-btn>
        </v-card-actions>
      </v-card>
    </v-dialog>

  </v-main>
</template>
<script>
export default {
  name: "List",
  data() {
    return {
      activeColor: 'red',
      search: null,
      searchBy: null,
      dialog: false,
      dialogUpdate: false,
      dialogDelete: false,
      indexUpdate: null,
      indexDelete: null,
      headers: [
        {
          text: "Task",
          align: "start",
          sortable: true,
          value: "task",
        },
        { text: "Priority", value: "priority" },
        { text: "Actions", value: "actions" },
      ],
      todos: [
        {
          task: "bernafas",
          priority: "Penting",
          note: "huffttt",
        },
        {
          task: "nongkrong",
          priority: "Tidak penting",
          note: "bersama tman2",
        },
        {
          task: "masak",
          priority: "Biasa",
          note: "masak air 500ml",
        },
      ],
      formTodo: {
        task: null,
        priority: null,
        note: null,
      },
    };
  },
  methods: {
    save() {
      this.todos.push(this.formTodo);
      this.resetForm();
      this.dialog = false;
    },
    cancel() {
      this.resetForm();
      this.dialog = false;
      this.dialogUpdate = false;
      this.dialogDelete = false;
    },
    resetForm() {
      this.formTodo = {
        task: null,
        priority: null,
        note: null,
      };
    },
    update(item){
      this.dialogUpdate = true;
      this.indexUpdate = this.todos.indexOf(item);
      this.formTodo = {
        task: item.task,
        priority: item.priority,
        note: item.note,
      }
    },
    updateUp(){
      this.todos[this.indexUpdate].task = this.formTodo.task;
      this.todos[this.indexUpdate].priority = this.formTodo.priority;
      this.todos[this.indexUpdate].note = this.formTodo.note;
      this.dialogUpdate = false;
    },
    deleteItem(){
        this.dialogDelete = false;
        this.indexDelete = this.todos.indexOf(this.formTodo);
        this.todos.splice(this.indexDelete, 1);
    },
    deleteUp(item){
        this.dialogDelete = true;
        this.formTodo = item;
    },
    setColor(priority){
      if(priority == "Penting"){
        return 'red';
      } else if(priority == 'Biasa'){
        return 'blue';
      }else{
        return 'green';
      }
    },
    sortBy(){
        let finds = this.sorting;
        var fil = this.todos.filter(function(x){
            return x.priority == finds;
        })
        return fil;
    }
  },
};
</script>