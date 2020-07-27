<template>
  <v-container>
    <v-data-table
      :headers="headers"
      :items="todos"
      item-key="id"
      sort-by="id"
      class="elevation-1"
    >
      <template v-slot:top>
        <v-toolbar flat color="white">
          <v-spacer></v-spacer>
          <v-dialog v-model="dialog" max-width="500px">
            <template v-slot:activator="{ on, attrs }">
              <v-btn color="primary" dark class="mb-2" v-bind="attrs" v-on="on"
                >New Todo</v-btn
              >
            </template>

            <v-card>
              <v-card-title>
                <span class="headline">{{ formTitle }}</span>
              </v-card-title>

              <v-card-text>
                <v-container>
                  <v-row>
                    <v-col cols="12" sm="12" md="12">
                      <v-text-field
                        v-model="editedItem.title"
                        label="Title"
                      ></v-text-field>
                    </v-col>
                  </v-row>
                </v-container>
              </v-card-text>

              <v-card-actions>
                <v-spacer></v-spacer>
                <v-btn color="blue darken-1" text @click="close">Cancel</v-btn>
                <v-btn color="blue darken-1" text @click="save">Save</v-btn>
              </v-card-actions>
            </v-card>
          </v-dialog>
        </v-toolbar>
      </template>

      <template v-slot:item.title="{ item }">
        <span
          :style="{ 'text-decoration': item.completed ? 'line-through' : '' }"
          >{{ item.title }}</span
        >
      </template>

      <template v-slot:item.completed="{ item }">
        <v-simple-checkbox v-model="item.completed"></v-simple-checkbox>
      </template>

      <template v-slot:item.actions="{ item }">
        <v-icon small class="mr-2" @click="editItem(item)">mdi-pencil</v-icon>
        <v-icon small @click="deleteItem(item)">mdi-delete</v-icon>
      </template>

      <template v-slot:no-data>
        <v-btn color="primary" @click="initialize">Reset</v-btn>
      </template>
    </v-data-table>
  </v-container>
</template>

<script>
import mockData from "./mockData.json";

export default {
  name: "Todo",

  data() {
    return {
      dialog: false,
      headers: [
        { text: "Done", value: "completed" },
        { text: "Title", value: "title" },
        { text: "Actions", value: "actions", sortable: false }
      ],
      todos: [],
      editedIndex: -1,
      editedItem: {
        id: 0,
        title: "",
        completed: false
      },
      defaultItem: {
        id: 0,
        title: "",
        completed: false
      }
    };
  },

  computed: {
    formTitle() {
      return this.editedIndex === -1 ? "New Todo" : "Edit Todo";
    }
  },

  watch: {
    dialog(val) {
      val || this.close();
    }
  },

  created() {
    this.initialize();
  },

  methods: {
    initialize() {
      this.todos = mockData;
    },

    editItem(item) {
      this.editedIndex = this.todos.indexOf(item);
      this.editedItem = Object.assign({}, item);
      this.dialog = true;
    },

    deleteItem(item) {
      const index = this.todos.indexOf(item);
      confirm("Are you sure you want to delete this todo item?") &&
        this.todos.splice(index, 1);
    },

    close() {
      this.dialog = false;
      this.$nextTick(() => {
        this.editedItem = Object.assign({}, this.defaultItem);
        this.editedIndex = -1;
      });
    },

    save() {
      if (this.editedIndex > -1) {
        Object.assign(this.todos[this.editedIndex], this.editedItem);
      } else {
        const lastIndex = this.todos[this.todos.length - 1].id;
        this.editedItem.id = lastIndex + 1;
        this.todos.push(this.editedItem);
      }
      this.close();
    }
  }
};
</script>
