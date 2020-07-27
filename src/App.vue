<template>
  <v-app>
    <v-app-bar app color="primary" dark>
      <div class="d-flex align-center">
        <h1 class="display-1 font-weight-bold">Todo</h1>
      </div>
    </v-app-bar>
    <v-main>
      <Todo />
    </v-main>
  </v-app>
</template>

<script lang="ts">
import Vue from "vue";
import Todo from "./components/Todo.vue";

const App = Vue.extend({
  name: "App",

  components: {
    Todo
  }
});

export default App;
</script>
