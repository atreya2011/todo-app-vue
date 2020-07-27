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

<script>
import Todo from "./components/Todo.vue";

export default {
  name: "App",

  components: {
    Todo
  }
};
</script>
