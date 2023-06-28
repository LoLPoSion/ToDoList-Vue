<template>
  <div id="app">
    <h1 class="title">{{ title }}</h1>
    <div class="todo">
      <input v-model="text" type="text" placeholder="Пиши задачи на день" />
      <button @click="addTodo()" class="btn">Добавить задачу</button>
      <div v-for="(todo, i) in todos" :key="todo.id">
        <p>
          <input v-model="todo.completed" type="checkbox" class="todo__check" />
          <span class="todo__id">{{ i + 1 }}. </span>
          <span class="todo__text" v-if="!todos[i].edit" :class="{ todo__text_complete: todo.completed }"
            @dblclick="editTodo(i)">
            {{ todo.todo }}<button @click="deleteTodo(i)" class="btn_del">+</button></span>
          <span v-else>
            <input v-model="todos[i].todo" type="text" :placeholder="todo.todo" />
            <button class="btn__submit" @click="todos[i].edit = false; saveEdit()">Save</button>
          </span>
        </p>
      </div>
      <div class="todo__limit">
        Сколько вывести <input v-model="limitNumber" type="number" min="1" max="150" />
        Сколько пропустить <input v-model="skipNumber" type="number" min="1" max="150" />
        <button @click="addDummy"> Добавить дела из API</button>
      </div>
    </div>
  </div>
</template>

<script>
export default {
  data() {
    return {
      title: "Список дел",
      text: "",
      todos: [],
      complete: false,
      editText: "",
      limitNumber: "",
      skipNumber: ""
    };
  },
  mounted() {
    const data = localStorage.getItem('todos')
    this.todos = data ? JSON.parse(data) : [];
  },
  methods: {
    addTodo() {
      if (this.text != "") {
        this.todos.push({
          id: Date.now(),
          todo: this.text,
          completed: this.complete,
          edit: false,
        })
        localStorage.setItem('todos', JSON.stringify(this.todos))
      }
      this.text = "";
    },
    deleteTodo(index) {
      this.todos.splice(index, 1)
      localStorage.setItem('todos', JSON.stringify(this.todos))
    },
    editTodo(index) {
      this.todos[index].edit = true;
    },
    saveEdit() {
      localStorage.setItem('todos', JSON.stringify(this.todos))
    },
    addDummy() {
      fetch(`https://dummyjson.com/todos?limit=${this.limitNumber}&skip=${this.skipNumber}`)
        .then(res => res.json())
        .then(data => {
          data.todos.forEach(element => { this.todos.push({ ...element, edit: false }) })
          localStorage.setItem('todos', JSON.stringify(this.todos))
        });
    }
  }
}

</script>

<style lang="scss">
#app {
  font-family: Avenir, Helvetica, Arial, sans-serif;
  -webkit-font-smoothing: antialiased;
  -moz-osx-font-smoothing: grayscale;
  color: #2c3e50;
  margin-top: 20px;
  display: flex;
  flex-direction: column;
  align-items: center;
}

.title {
  text-align: center;
}

.todo {
  width: 500px;
  height: 100%;
  background: #d89c58;
  padding: 30px;
  outline: 1px solid #000;
  min-height: 500px;
  border-radius: 9px;

  &__id {
    font-weight: bold;
  }

  &__text {
    font-size: 15px;
    color: #000;
    text-transform: capitalize;

    &_complete {
      color: grey;
      text-decoration: line-through;
    }

  }

  &__check {
    display: inline-block;
    margin-right: 5px;
  }

}

.btn_del {
  border-radius: 50%;
  outline: none;
  cursor: pointer;
  background: transparent;
  border: none;
  margin-left: auto;
  padding: 0;
  transform: rotate(45deg);
  font-size: 18px;
  text-align: center;
  opacity: 0.7;
  display: inline-block;
}
</style>
