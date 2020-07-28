<template>
  <v-container>
    <v-data-table
      :headers="state.headers"
      :items="state.todos"
      item-key="id"
      sort-by="id"
      class="elevation-1"
    >
      <template v-slot:top>
        <v-toolbar flat color="white">
          <v-spacer></v-spacer>
          <v-dialog v-model="state.dialog" max-width="500px">
            <template v-slot:activator="{ on, attrs }">
              <v-btn color="primary" dark class="mb-2" v-bind="attrs" v-on="on"
                >New Todo</v-btn
              >
            </template>

            <v-card>
              <v-card-title>
                <span class="headline">{{ state.formTitle }}</span>
              </v-card-title>

              <v-card-text>
                <v-container>
                  <v-row>
                    <v-col cols="12" sm="12" md="12">
                      <v-text-field
                        v-model="state.editedItem.title"
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
import { reactive, computed, watch } from "@vue/composition-api";

export default {
  setup(_, { root }) {
    // define state data
    const state = reactive({
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
      },
      formTitle: computed(() =>
        state.editedIndex === -1 ? "New Todo" : "Edit Todo"
      )
    });

    // define methods
    const initialize = () => {
      state.todos = mockData;
    };

    const editItem = item => {
      state.editedIndex = state.todos.indexOf(item);
      state.editedItem = Object.assign({}, item);
      state.dialog = true;
    };

    const deleteItem = item => {
      const index = state.todos.indexOf(item);
      confirm("Are you sure you want to delete state todo item?") &&
        state.todos.splice(index, 1);
    };

    const close = () => {
      state.dialog = false;
      root.$nextTick(() => {
        state.editedItem = Object.assign({}, state.defaultItem);
        state.editedIndex = -1;
      });
    };

    const save = () => {
      if (state.editedIndex > -1) {
        Object.assign(state.todos[state.editedIndex], state.editedItem);
      } else {
        const lastIndex = state.todos[state.todos.length - 1].id;
        state.editedItem.id = lastIndex + 1;
        state.todos.push(state.editedItem);
      }
      close();
    };

    watch(state.dialog, val => {
      val || state.close();
    });

    initialize();

    return { state, editItem, deleteItem, close, save };
  }
};
</script>
