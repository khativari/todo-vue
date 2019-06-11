<template>
  <div>
    <input type="text" class="todo-input" 
    placeholder="Whats need to be done?" 
    v-model="newTodo" @keyup.enter="addTodo">
    <transition-group name="fade" enter-active-class="animated fadeInUp" leave-active-class="animated fadeOutDown">
      <div v-for="(todo,index) in todosFiltered" :key="todo.id" class="todo-item">

        <div class="todo-item-left">
          <input type="checkbox" v-model="todo.completed">
          <div v-if="!todo.editing"@dblclick="editTodo(todo)" class="todo-item-label" :class="{ completed : todo.completed }">
            {{ todo.title }}
          </div>

          <input type="text" v-else class="todo-item-edit" 
          v-model="todo.title" @blur="doneEdit(todo)" v-focus
          @keyup.enter="doneEdit(todo)" 
          @keydown.escape="cancelEdit(todo)"
          @keydown.ctrl.enter="complete(todo)">

        </div>
        <div class="remove-item" @click="removeTodo(index)">
          &times;
        </div>      
      </div>
    </transition-group>

    <div class="extra-container">
      <div><label><input type="checkbox" :checked="!anyRemaining"
        @change="checkAllTodos">Check all</label></div>
        <div>{{ remaining }} items left</div>
      </div>

      <div class="extra-container">
        <div>
          <button class="flat xs green" :class="{ active: filter == 'all' }" @click="filter = 'all' ">All</button>
          <button class="flat xs green" :class="{ active: filter == 'active' }" @click="filter = 'active' ">Active</button>
          <button class="flat xs green" :class="{ active: filter == 'completed' }" @click="filter = 'completed' ">Completed</button>
        </div>

        <div>
          <transition name="fade">
            <button class="flat xs pink"  v-if="showClearCompletedButton"
            @click="clearCompleted">Clear All</button>
          </transition>
        </div>

      </div>
    </div>
  </template>

  <script>
    export default {
      name: 'todo-list',
      data () {
        return {
          newTodo: '',
          idForTodo: 3,
          beforeEditCache: '',
          filter: 'all',
          todos: [
          {
            'id': 1,
            'title': 'Finish Vue Screencast',
            'completed': false,
            'editing': false
          },
          {
            'id': 2,
            'title': 'Take over world',
            'completed': false,
            'editing': false
          },

          ]
        }
      },
      computed:{
        remaining(){
          return this.todos.filter(todo => !todo.completed).length
        },
        anyRemaining(){
          return this.remaining != 0
        },
        todosFiltered(){
          if(this.filter == 'all'){
            return this.todos

          }else if(this.filter == 'active'){
            return this.todos.filter(todo => !todo.completed)

          }else if(this.filter == 'completed'){
            return this.todos.filter(todo => todo.completed)
          }
          return this.todos
        },
        showClearCompletedButton(){
          return this.todos.filter(todo => todo.completed).length > 0
        }
      },
      directives:{
        focus:{
          inserted: function function_name(el) {
            el.focus()
          }
        }
      },
      methods:{
        addTodo(){
          if(this.newTodo.trim().length == 0){
            return
          }

          this.todos.push({
            id: this.idForTodo,
            title: this.newTodo,
            editing: false,
            completed: false,
          })

          this.newTodo = ''
          this.idForTodo++;
        },
        complete(todo){
          todo.completed = true
        },
        removeTodo(index){
          this.todos.splice(index,1)
        },
        editTodo(todo){
          this.beforeEditCache = todo.title
          todo.editing = true
        },
        doneEdit(todo){
          if(todo.title.trim() == ''){
            todo.title = this.beforeEditCache
          }
          todo.editing = false
        },
        cancelEdit(todo){
          todo.title = this.beforeEditCache
          todo.editing = false
        },
        checkAllTodos(){
          this.todos.forEach((todo) => todo.completed = event.target.checked)
        },
        clearCompleted(){
          return this.todos = this.todos.filter(todo => !todo.completed)
        }
      }
    }
  </script>

  <style src="./todo.css" lang="scss"></style>

