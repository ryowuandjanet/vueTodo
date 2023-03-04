<template>
  <div class="todo-container">
    <div class="todo-wrap">
      <Header :addTodo="addTodo" />
      <List :todos="todos" :deleteTodo="deleteTodo" :updateTodo="updateTodo" />
      <Footer
        :todos="todos"
        :checkAll="checkAll"
        :clearAllCompletedTodos="clearAllCompletedTodos"
      />
    </div>
  </div>
</template>

<script lang="ts">
import { defineComponent, watch, onMounted, reactive, toRefs } from "vue";
import Header from "./components/Header.vue";
import List from "./components/List.vue";
import Footer from "./components/Footer.vue";
import { Todo } from "./types/todo";
import { saveTodos, readTodos } from "./utils/localStorageUtils";

export default defineComponent({
  name: "App",
  components: {
    Header,
    List,
    Footer,
  },
  setup() {
    const state = reactive<{ todos: Todo[] }>({
      todos: [],
      // todos: [
      //   { id: 1, title: "跑步", isCompleted: false },
      //   { id: 2, title: "買飯", isCompleted: true },
      //   { id: 3, title: "上課", isCompleted: true },
      // ],
    });

    onMounted(() => {
      setTimeout(() => {
        state.todos = readTodos();
      }, 1000);
    });

    const addTodo = (todo: Todo) => {
      state.todos.unshift(todo);
    };

    const deleteTodo = (index: number) => {
      state.todos.splice(index, 1);
    };

    const updateTodo = (todo: Todo, isCompleted: boolean) => {
      todo.isCompleted = isCompleted;
    };

    const checkAll = (isCompleted: boolean) => {
      state.todos.forEach((todo) => {
        todo.isCompleted = isCompleted;
      });
    };

    const clearAllCompletedTodos = () => {
      state.todos = state.todos.filter((todo) => !todo.isCompleted);
    };

    watch(() => state.todos, saveTodos, { deep: true });

    return {
      ...toRefs(state),
      addTodo,
      deleteTodo,
      updateTodo,
      checkAll,
      clearAllCompletedTodos,
    };
  },
});
</script>

<style>
/*app*/
.todo-container {
  width: 600px;
  margin: 0 auto;
}
.todo-container .todo-wrap {
  padding: 10px;
  border: 1px solid #ddd;
  border-radius: 5px;
}
</style>
