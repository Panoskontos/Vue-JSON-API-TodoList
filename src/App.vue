<template>
  <div>
    <h1>Todos</h1>
    <input type="text" v-model="todoName" @keyup.enter="createObject">
    <ul>
      <!-- animation -->
      <transition-group enter-active-class="animate__animated animate__backInUp" leave-active-class="animate__animated animate__bounceOut">
      <li v-for="todo of todos" :key="todo.id">
        <div class="normal-mode">
          {{todo.name}} 
          <i v-on:click="showEdit(todo.id)" class="bi bi-pencil"></i>
           <i v-on:click="deleteObject(todo.id)" class="bi bi-trash2"></i>
        </div>
        <div class="edit-mode invisible">
          <input type="text" v-model="editName" @keyup.enter="editObject(todo.id)">
        </div>
      </li>
      </transition-group>
    </ul>
  </div>
</template>

<script>
import axios from 'axios';

const baseURL = 'http://localhost:3000/todos'

export default {
  name: 'App',
  data(){
    return {
      todos: [],
      todoName: '',
      editName: '',
    }
  },
  async created(){
    try {
      const res = await axios.get(baseURL);
      this.todos = res.data;
    } catch(e) {
      console.error(e)
    }
  },
  methods: {
    // CREATE
    async createObject(){
      // post request
      const res = await axios.post(baseURL, {name: this.todoName});
      // concatinate in our data
      this.todos.push(res.data)

      this.todoName = ''
    },
    // DELETE
    async deleteObject(id){
      // if (confirm('Are you sure you want to delete?')){
        const res = await axios.delete(baseURL + '/'+id);
        this.todos.splice(id,1)
        console.log(res)
      // }
    },
    // EDIT
    showEdit(id){
      console.log(id)
      document.querySelectorAll('li')[id].querySelector('.edit-mode').classList.remove('invisible')
      document.querySelectorAll('li')[id].querySelector('.normal-mode').classList.add('invisible')
     
    },
    async editObject(id){
    // patch request
      const res = await axios.patch(baseURL + '/'+id, {name: this.editName});
      console.log(res.data)
      this.todos[id] = res.data
      this.editName = ''
      document.querySelectorAll('li')[id].querySelector('.edit-mode').classList.add('invisible')
      document.querySelectorAll('li')[id].querySelector('.normal-mode').classList.remove('invisible')
     
    }
    
  }

}
</script>

<style>
li {
  list-style: none;
  background-color: blueviolet;
  width: 200px;
  color: white;
  margin: 10px;
  padding: 5px;
  border-radius: 10px;
  text-align: center;
  position: relative;
}

.bi-trash2 {
  position: absolute;
  right: 10px;
  cursor: pointer;
}

.bi-pencil {
  position: absolute;
  left: 15px;
  cursor: pointer;
}
.edit-mode input{
  background-color: blueviolet;
  color: white;
}

.invisible {
  display: none;
}


</style>
