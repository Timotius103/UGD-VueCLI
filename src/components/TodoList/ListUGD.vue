<template>
  <v-main class="list">
    <h3 class="text-h3 font-weight-medium mb-5">To Do List</h3>

    <v-card>
      <v-card-title primary-title>
        <v-text-field
            v-model="search"
            append-icon="mdi-magnify"
            label="search"
            single-line
            hide-details></v-text-field>
        <v-spacer></v-spacer>
        <v-select
            :items="['All Priority','Penting', 'Biasa', 'Tidak Penting']"
            v-model="filters"
            label="Priority"
            @change="filterPriority"
            outlined
            hide-details
            class="mr-4"
            dense></v-select>
        <v-btn color="success" dark @click="dialog = true">Tambah</v-btn>
      </v-card-title>

      <v-data-table
          :headers="headers"
          :items="filterPriority"
          :search="search" pagination.sync="pagination">

          <template v-slot:[`item.priority`]="{ item }">
            <td>
              <v-card v-if="item.priority == 'Penting'" style="border-color: lightcoral; color: lightcoral; width: fit-content;" outlined>
                {{ item.priority }}
              </v-card>
              <v-card v-else-if="item.priority == 'Biasa'" style="border-color: lightblue; color: lightblue; width: fit-content;" outlined>
                {{ item.priority }}
              </v-card>
              <v-card v-else outlined style="border-color: lightgreen; color: lightgreen; width: fit-content;">
                {{ item.priority }}
              </v-card>
            </td>
          </template>
<!--          <template>-->
<!--            <v-row justify="center">-->
<!--              <v-expansion-panels accordion>-->
<!--                <v-expansion-panel v-for="(item,i) in 5" :key="i">-->
<!--                  <v-expansion-panel-header>Item</v-expansion-panel-header>-->
<!--                  <v-expansion-panel-content>-->
<!--                    Lorem ipsum dolor sit amet, consectetur adipiscing elit, sed do eiusmod tempor incididunt ut labore et dolore magna aliqua. Ut enim ad minim veniam, quis nostrud exercitation ullamco laboris nisi ut aliquip ex ea commodo consequat.-->
<!--                  </v-expansion-panel-content>-->
<!--                </v-expansion-panel>-->
<!--              </v-expansion-panels>-->
<!--            </v-row>-->
<!--          </template>-->
          <template v-slot:[`item.actions`]="{ item }">
            <v-btn small class="mr-2" @click="editItem(item)">
              edit
            </v-btn>
            <v-btn small @click="deleteItem(item)">
              delete
            </v-btn>
          </template>
      </v-data-table>
    </v-card>

<!--    <v-dialog v-model="dialogdel" persistent max-width="400px">-->
<!--      <v-card>-->
<!--        <v-card-title>-->
<!--                    <span class="headline">-->
<!--                        Yakin ingin menghapus?-->
<!--                    </span>-->
<!--        </v-card-title>-->

<!--        <v-card-actions>-->
<!--          <v-spacer></v-spacer>-->

<!--          <v-btn color="green darken-1" text @click="cancel">-->
<!--            Tidak-->
<!--          </v-btn>-->

<!--          <v-btn color="red darken-1" text @click="confirmdelete">-->
<!--            Ya-->
<!--          </v-btn>-->

<!--        </v-card-actions>-->

<!--      </v-card>-->
<!--    </v-dialog>-->

    <v-dialog
        v-model="dialog"
        persistent
        max-width="600px"
        transition="dialog-transition">
      <v-card>
        <v-card-title>
          <span class="headline">From Todo</span>
        </v-card-title>
        <v-card-text>
          <v-container>
            <v-text-field
                v-model="formTodo.task"
                label="Task"
                required
            ></v-text-field>
            <v-select
                :items="['Penting', 'Biasa', 'Tidak Penting']"
                v-model="formTodo.priority"
                label="All Priority"
                required
            ></v-select>
            <v-textarea
                v-model="formTodo.note" label="Note" required>
            </v-textarea>
          </v-container>
        </v-card-text>
        <v-card-actions>
          <v-spacer></v-spacer>
          <v-btn color="blue darken-1" text @click="cancel">Cancel</v-btn>
          <v-btn color="blue darken-1" text @click="save">Save</v-btn>
        </v-card-actions>
      </v-card>
    </v-dialog>
  </v-main>
</template>
<script>
export default {
  name:"List",
  data(){
    return {
      search:null,
      dialog:false,
      tempTodo:null,
      filters:"All Priority",
      dialogdel: false,
      selected: [],
      headers: [
        {
          text:'Task',
          align: 'start',
          sortable: true,
          value: 'task',
        },
        { text: "Priority", value:"priority"},
        { text: "Note", value:"note"},
        { text: "Actions", value:"actions"},
      ],
      todos: [
        {
          task: "Tidur",
          priority: "Penting",
          note: "Enak",
        },
        {
          task: "Gibah",
          priority: "Tidak Penting",
          note: "Merusak bangsa"
        },
      ],
      formTodo:  {
        task: null,
        priority: null,
        note: null,
      },
    };
  },
  methods: {
    save(){
      this.todos.push(this.formTodo);
      this.resetForm();
      this.dialog = false;
    },
    cancel() {
      this.resetForm();
      this.dialog = false;
      this.edititem = null;
      this.adding = true;
      this.dialogdel = false;
      this.dialognote = false;
    },
    resetForm(){
      this.formTodo = {
        task: null,
        priority: null,
        note: null,
      };
    },

    editItem(item){
      this.formTodo = item;
      this.dialog = true;
    },
    deleteItem(item){
      let x = window.confirm("Apa yakin ingin menghapus?  ");
      if(x === true){
        let index = this.findIndexTodos(item);
        this.todos.splice(index,1);
      }
    },
    findIndexTodos(item){
      return this.todos.findIndex(obj => obj.task === item.task);
    },

  },
  computed: {
    filterPriority(){
      let finds = this.filters;
      if(finds == "All Priority"){
        return this.todos;
      }else {
        var fil = this.todos.filter(function(x){
          return x.priority == finds;
        })
        return fil;
      }

    },
  }

};
</script>