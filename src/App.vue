<template >
  <div id="app" class="container my-3">
    <div class="input-group mb-3">
      <div class="input-group-prepend">
        <span class="input-group-text" id="basic-addon1">待辦事項</span>
      </div>
      <input type="text" class="form-control" placeholder="準備要做的任務" v-model="newTodo" @keyup.enter="addTodo">
      <div class="input-group-append">
        <button class="btn btn-primary" type="button" @click="addTodo">新增</button>
      </div>
    </div>
    <div class="card text-center">
      <div class="card-header">
        <ul class="nav nav-tabs card-header-tabs">
          <li class="nav-item">
            <a class="nav-link" :class="{'active' : visibility==='all'}" @click.prevent="visibility= 'all'" href="#">全部</a>
          </li>
          <li class="nav-item">
            <a class="nav-link " :class="{'active' : visibility==='active'}" @click="visibility= 'active'" href="#">進行中</a>
          </li>
          <li class="nav-item">
            <a class="nav-link" :class="{'active' : visibility==='completed'}" @click="visibility= 'completed'" href="#">已完成</a>
          </li>
        </ul>
      </div>
      <ul class="list-group list-group-flush text-left">
        <li class="list-group-item" v-for="(item) in filteredTodos" :key="item.id" @dblclick="editTodo(item)">
          <div class="d-flex" v-if="item.id !== cacheTodo.id">
            <div class="form-check">
              <input type="checkbox" class="form-check-input" v-model="item.completed" :id="item.id" @click="setStorage">
              <label class="form-check-label" :for="item.id"
                     :class="{'classcompleted' : item.completed}">
                {{item.title}}
              </label>
            </div>
            <button type="button" class="close ml-auto" aria-label="Close" @click="removeTodo(item)">
              <span aria-hidden="true">&times;</span>
            </button>
          </div>
          <input type="text" class="form-control" v-if="item.id === cacheTodo.id" v-model="cacheTitle" @keyup.esc="cancelEdit()" @keyup.enter ="doneEdit(item)">
        </li>
        <li class="list-group-item">
          <input type="text" class="form-control">
        </li>
      </ul>
      <div class="card-footer d-flex justify-content-between">
        <span>還有 {{countActive}} 筆任務未完成</span>
        <a href="#" @click.prevent="removeAll">清除所有任務</a>
      </div>
    </div>
  </div>
</template>

<script>


export default {
  name: 'App',
  data(){
    return{
      newTodo:'',
      todos: [
        {
          id:Math.floor(Date.now()),
          title: 'demo',
          completed: false,
        }
      ],
      cacheTodo :{},
      cacheTitle:'',
      visibility: 'all',
      counter:0,
    }
  },
  created() {
    this.getStorage();
  },
  methods:{
    addTodo:function(){
      let value = this.newTodo.trim();
      let timestamp = Math.floor(Date.now());
      if(!value){return;}
      this.todos.push({
        id: timestamp,
        title: value,
        completed: false,
      })
      this.newTodo = '';
      this.setStorage();
    },
    removeTodo: function(todo){
      let vm = this;
      let newIndex = vm.todos.findIndex(function(item){
        return todo.id === item.id;
      })
      this.todos.splice(newIndex,1);
      this.setStorage();
    },
    editTodo: function(item){
      this.cacheTodo = item;
      this.cacheTitle = item.title;
    },
    cancelEdit:function(){
      this.cacheTodo = {};
    },
    doneEdit:function(item){
      item.title = this.cacheTitle;
      this.cacheTitle ='';
      this.cacheTodo = {};
      this.setStorage();
    },
    removeAll:function(){
      console.log(this.todos);
      this.todos=[];
      this.setStorage();
    },
    setStorage:function(){
      console.log(this.todos);
      localStorage.setItem('app', JSON.stringify(this.todos));
      return console.log('stored');
    },
    getStorage:function(){
      this.todos = JSON.parse(localStorage.getItem('app')) || [];
      return console.log('got');
    },
  },
  computed:{
    filteredTodos :function(){
      if(this.visibility === 'all'){
        return this.todos;
      }else if(this.visibility === 'active'){
        let newTodos = [];
        this.todos.forEach(function(item){
          if(!item.completed){
            newTodos.push(item);
          }
        })
        return newTodos;
      }else if(this.visibility==='completed'){
        let newTodos = [];
        this.todos.forEach(function(item){
          if(item.completed){
            newTodos.push(item);
          }
        })
        return newTodos;
      }
      return [];
    },
    countActive:function(){
      let counter = 0;
      for(let i = 0; i<this.todos.length; i++){
        if(this.todos[i].completed === false){
          counter++;
        }
      }
      return counter;
    },

  }


}
</script>

<style lang="scss">
@import "~bootstrap/scss/bootstrap";
</style>
