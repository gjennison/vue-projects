<template>
    <div id="app">
    
    <div id="searchContainer">
      <input type="text" id="item" autocomplete="off" placeholder="item" v-model="newTodo" v-on:keyup.enter="submit">
      <div class="buttons">
        <button @click="addItem" v-bind:class="{activeButton: add}"> add </button> &nbsp;
        <button @click="add=false" v-bind:class="{activeButton: !add}"> search </button>
      </div>
    </div>
    <ul>
      <li v-for="todo in todos" v-bind:key="todo.id">
        <div v-show="add" v-bind:class="{completed: todo.completed}" @mousedown="todo.completed=!todo.completed">
          <span></span>
          <p>{{todo.title}}</p>
          <span @click="removeTodo(todo)">X</span>
        </div>

        <div v-show="!add && todo.title.includes(newTodo.toLowerCase())" v-bind:class="{completed: todo.completed}" @mousedown="todo.completed=!todo.completed" class="center">
          <span></span>
          <p>{{todo.title}}</p>
          <span @click="removeTodo(todo)">X</span>
        </div>
      </li>
    </ul>
    </div>
</template>

<script>
export default {
     data() {
    return {
      todos: [
        {
          title: "walk riz",
          completed: false
        },
        {
          title: "eat breakfast",
          completed: true
        },
        {
          title: "go to whitianga",
          completed: false
        },
        {
          title: "play violin",
          completed: false
        },
        {
          title: "read Dickens",
          completed: false
        }
      ],
      newTodo: "",
      add: true,
    }
  },

  methods:{
    submit: function(){
      if(this.newTodo != ""){
        this.todos.push({
          title: this.newTodo,
          completed: false
        })
      }
      this.newTodo=""
    },

    removeTodo: function(todo){
      this.todos.splice(this.todos.indexOf(todo), 1)
    },

    addItem: function(){
      if(this.add)
        this.submit()
      else
        this.add = true
    }
  }
}
</script>

<style scoped>
#app{
  margin-top: 30px;
  margin-left: 25%;
  margin-right: 25%;
}

@media (max-width: 1100px) {
  #app{
    margin-top: 30px;
  } 
}

button{
  text-decoration: none;
  background-color: transparent;
  border: 2px solid white;
  font-size: 1.2em;
  padding: .45em;
  color: white;
  transition: all 0.5s;
  border-radius: 10%;
}

button:hover{
  background-color: white;
  color: black;
  border-radius: 10%;
}

button:focus{
  outline: 0;
}

.activeButton{
  color: #11998e;
}

.completed{
  background-color: rosybrown;
  opacity: 0.5;
}

.center{
  display: flexbox;
  justify-content: space-between;
}

p{
  margin: 0;
}

.completed p{
  text-decoration: line-through;
}

ul{
  list-style: none;
  padding-left: 0;
  font-size: 2em;
}

li div{
  background-color: #11998e;
  transition: all 0.3s;
  display: flex;
  justify-content: space-between;
}

li div:hover{
  background-color: aliceblue;
}

li div:hover span{
  opacity: 1;
}

span{
  color: #11998e;
  font-size: .6em;
  padding-top: 0.25em;
  margin-right: 0.3em;
  opacity: 0;
  transition: all 0.2s;
}

input{
    background: transparent;
    color: white;
    border-bottom: 2px solid white;
    border-left: 0px;
    border-top: 0px;
    border-right: 0px;
    font-size: 2em;
    transition: border-color 0.2s;
    outline: none;
    float: left;
}

input:focus{
    border-color: #11998e;
    border-left: 0px;
    border-top: 0px;
}

.submit_label{
    position: relative;
    top: -2em;
    display: block;
    color: white;
    transition: 0.2s;
    font-size: 1em;
}

#searchContainer{
  display: flex;
  justify-content: space-evenly;
  margin-bottom: 40px;
}

button{
    margin-left: 10px;
}
</style>