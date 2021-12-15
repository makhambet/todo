<template>
  <section class="todo">
    <header class="todo__header">
      <h1 class="todo__title">TODO</h1>
    </header>

    <form @submit.prevent="createTask()" class="todo__form todo__wrapper">
      <input
        v-model="newTodo"
        autofocus
        class="todo__input"
        type="text"
        name="task"
        placeholder="Вставьте свою задачу..."
      >
    </form>

    <ul class="todo__list todo__wrapper">
      <li
        v-for="(task, index) in filteredTodos"
        :key="index"
        class="todo__task"
        :class="{'todo__task_complete': task.completed, 'todo__task_hide': !task.show}"
      >
        <label class="todo__list-toggle">
          <input @change="toggleComplete(task)" v-model="task.completed" type="checkbox" hidden>
          <span></span>
        </label>
        <p @dblclick="editTask(task, index)" class="todo__task-text">{{ task.title }}</p>
        <button @click="removeTask(index)" class="todo__task-remove">remove</button>
        <input
          :ref="'task-' + index"
          v-show="task.edit"
          class="todo__task-edit"
          type="text"
          v-model.trim="task.title"
          @blur="updateEdit(task)"
          @keyup.enter="updateEdit(task)"
        />
      </li>
    </ul>
    <footer class="todo__footer todo__wrapper">
      <ul class="todo__tabs">
        <li @click="showType='all'" :class="{'todo__tab_active': showType === 'all'}" class="todo__tab">
          Все
        </li>
        <li @click="showType='active'" :class="{'todo__tab_active': showType === 'active'}" class="todo__tab">
          Активные
        </li>
        <li @click="showType='completed'" :class="{'todo__tab_active': showType === 'completed'}" class="todo__tab">
          Выполненные
        </li>
      </ul>
    </footer>
  </section>
</template>

<script>
const filters = {
  all: function(todos) {
    return todos;
  },
  active: function(todos) {
    return todos.filter(function(todo) {
      return !todo.completed;
    });
  },
  completed: function(todos) {
    return todos.filter(function(todo) {
      return todo.completed;
    });
  }
}
export default {
  data() {
    return {
      list: JSON.parse(localStorage.getItem('todo') || '[]'),
      newTodo: '',
      showType: 'all'
    }
  },
  computed: {
    filteredTodos: function() {
      return filters[this.showType](this.list)
    }
  },
  methods: {
    createTask () {
      this.list.push({
        title: this.newTodo,
        completed: false,
        edit: false,
        show: true
      })
      this.newTodo = ''
    },
    toggleComplete () {
      // task.completed = !task.completed
    },
    removeTask (index) {
      this.list[index].show = false
      setTimeout(() => {
        this.list.splice(index, 1)
      }, 400)
    },
    editTask (task, index) {
      task.edit = true
      setTimeout(() => {
        this.$refs['task-' + index][0].focus()
      }, 10)
    },
    updateEdit (task) {
      task.edit = false
    }
  },
  watch: {
    list () {
      localStorage.setItem('todo', JSON.stringify(this.list))
    }
  },
}
</script>

<style scoped>
.todo__title {
  font-weight: 700;
  text-align: center;
  font-size: 64px;
  margin: 80px 0 25px;
}
.todo__wrapper {
  width: 800px;
  margin: auto;
}
.todo__form {
  margin-bottom: 20px;
}
.todo__input {
  width: 100%;
  height: 60px;
  background: none;
  border-bottom: 1px solid var(--main-color);
  padding: 20px;
  font-size: 28px;
  font-weight: 300;
}
.todo__task {
  width: 100%;
  min-height: 60px;
  padding: 20px;
  position: relative;
  color: #333;
  transition: all .2s ease;
  box-sizing: border-box;
}
.todo__task:hover {
  background: rgba(0, 0, 0, .04);
  cursor: pointer;
}
.todo__task_complete {
  text-decoration: line-through;
  color: #95a5a6;
}
.todo__task_hide {
  opacity: 0;
}
.todo__list-toggle {
  cursor: pointer;
}
.todo__list-toggle span {
  position: absolute;
  width: 20px;
  height: 20px;
  border-radius: 50% 50%;
  border: 1px solid #BDBDBD;
  top: 50%;
  transform: translateY(-50%);
}
.todo__list-toggle span:before {
  content: '';
  border: 1px solid aqua;
  border-top: none;
  border-right: none;
  position: absolute;
  width: 10px;
  height: 5px;
  top: 10%;
  left: 50%;
  transform: rotate(-45deg) translate(-50%, -10%);
  opacity: 0;
}
.todo__list-toggle input:checked ~ span:before {
  opacity: 1;
}
.todo__task-text {
  margin: 0;
  padding: 0 65px 0 30px;
  word-break: break-word;
}
.todo__task-remove {
  opacity: 0;
  position: absolute;
  right: 20px;
  top: 50%;
  transform: translateY(-50%);
  text-decoration: none;
  color: #7f8c8d;
  transition: all .2s ease;
  background: none;
}
.todo__task:hover .todo__task-remove {
  opacity: 1;
}
.todo__task-remove:hover {
  color: #333;
}
.todo__task-edit {
  border: 1px solid var(--main-color);
  height: 100%;
  position: absolute;
  z-index: 2;
  top: 0;
  right: 0;
  background: #EEEEEE;
  width: calc(100% - 50px);
  font-size: 16px;
}
.todo__tabs {
  display: flex;
  margin: 20px 0;
  justify-content: center;
  color: #333;
}
.todo__tab {
  padding: 2px 5px;
  border-radius: 3px;
  cursor: pointer;
  box-sizing: border-box;
  border: 1px solid #ecf0f1;
}
.todo__tab_active, .todo__tab:hover {
  border-color: var(--main-color);
}
.todo__tab:not(:last-child) {
  margin-right: 10px;
}
</style>
