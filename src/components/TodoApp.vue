<template>
  <div>
    <div id="new-task">
      <input type="text" class="title" placeholder="Task Title" @keyup.enter="addTask" v-model="newTaskTitle">
      <textarea name="task-desc" id="task-desc" rows="5" placeholder="Task description" v-model="newTaskDesc"></textarea>
    </div>
    <div id="tasks">
      <div v-for="todo in todos" :key="todo.id" class="task">
        <span class="title">{{ todo.title }}</span>
        <span class="desc">{{ todo.desc }}</span>
        <span class="delete" @click="removeTaks(todo.task_id)"><i class="far fa-trash-alt"></i></span>
        <span class="edit"><i class="far fa-edit" @click="openEditor(todo.task_id)"></i></span>
      </div>
    </div>

    <!-- edit form  -->
    <div id="editor-overlay" class="overlay" @click="hideEditor()"></div>
    <div id="edit-task">
      <input type="text" class="title" placeholder="Task Title" @keyup.enter="addTask" v-model="editTaskTitle">
      <textarea name="task-desc" id="task-desc" rows="15" placeholder="Task description" v-model="editTaskDesk"></textarea>
      <span class="delete" @click="removeCurrentTaks()"><i class="far fa-trash-alt"></i></span>
      <span class="save btn btn-success" @click="updateCurrentTask()">Save</span>
    </div>
  </div>
</template>

<script>
import db from '../firebase';
export default {
  name: 'todo-app',
  data () {
    return {
      newTaskTitle: "",
      newTaskDesc: "",
      editTaskTitle: "",
      editTaskDesk: "",
      editId: 0,
      nextId: 0,
      todos : []
    }
  },
  created () {
    this.updateTasks();
  },
  methods: {
    updateTasks() {
      db.collection('tasks').orderBy('task_id').get()
      .then(res => {
        this.todos = [];
        res.forEach(doc => {
          const data = {
            id: doc.id,
            task_id: doc.data().task_id,
            title: doc.data().title,
            desc: doc.data().desc,
            completed: doc.data().completed
          };
          console.log(data);
          this.todos.push(data);
          this.nextId = doc.data().task_id + 1;
        });
      });
    },
    addTask() {
      if (this.newTaskTitle) {
        db.collection('tasks').add({
          task_id: this.nextId,
          title: this.newTaskTitle,
          desc: this.newTaskDesc,
          completed: false
        }).then(() => {
          this.updateTasks();
          this.newTaskTitle = '';
          this.newTaskDesc = '';
        }).catch(e =>{
          console.log(e);
        });
      } else {
        alert("Title can not be empty");
      }
    },
    removeTaks(id){
      console.log(id);
      if(confirm("Are you sure you want to delete this item : ", id)){
        db.collection('tasks').where('task_id', '==', id).get()
        .then(res => {
          res.forEach(doc => {
            doc.ref.delete();
            this.updateTasks();
          })
        })
      }
    },
    openEditor(id) {
      db.collection('tasks').where('task_id', '==', id).get()
      .then(res => {
        res.forEach(doc => {
          this.editTaskTitle = doc.data().title;
          this.editTaskDesk = doc.data().desc;
          this.editId = doc.data().task_id;
        });
        document.getElementById("edit-task").classList.add('show');
        document.getElementById("editor-overlay").classList.add('show');
      })
    },
    hideEditor() {
      document.getElementById("editor-overlay").classList.remove('show');
      document.getElementById("edit-task").classList.remove('show');
      this.editTaskTitle = '';
      this.editTaskDesk = '';
    },
    removeCurrentTaks() {
      this.removeTaks(this.editId);
      this.hideEditor();
    },
    updateCurrentTask() {
      db.collection('tasks').where('task_id', '==', this.editId).get()
      .then(res => {
        res.forEach(doc => {
          doc.ref.update({
            title: this.editTaskTitle,
            desc: this.editTaskDesk
          });
        });
        this.hideEditor();
        this.updateTasks();
      })
    }
  },
}
</script>
<style scoped>
input, textarea {
  width: 100%;
  display: block;
  border: 0;
  outline: 0;
  margin: 0;
  padding: 0;
  font-size: 16px;
  background-color: transparent;
  resize:none;
}


#new-task{
  position: relative;
  width: 100%;
  max-width: 600px;
  margin: 0 auto;
  padding: 20px;
  background-color: #fff;
  border-radius: 8px;
  z-index: 10;

  -webkit-box-shadow: 0px 0px 5px 0px rgba(0,0,0,0.31);
  -moz-box-shadow: 0px 0px 5px 0px rgba(0,0,0,0.31);
  box-shadow: 0px 0px 5px 0px rgba(0,0,0,0.31);
}

#edit-task {
  display: none;
  position: absolute;
  top: 10%;
  left: 15%;
  right: 15%;
  z-index: 12;
  width: 70%;
  padding: 20px;
  background-color: #fff;
  border-radius: 8px;

  -webkit-box-shadow: 0px 0px 5px 0px rgba(0,0,0,0.31);
  -moz-box-shadow: 0px 0px 5px 0px rgba(0,0,0,0.31);
  box-shadow: 0px 0px 5px 0px rgba(0,0,0,0.31);
}
#edit-task.show{
  display: block;
}

div.overlay.show{
  position: absolute;
  top: 0;
  left: 0;
  height: 100%;
  width: 100%;
  background: rgba(0, 0, 0, 0.7);
  z-index: 11;
  transition-duration: 500ms;
}
div.overlay{
  transition-duration: 500ms;
  background: rgba(0, 0, 0, 0);
}
#new-task .title,#edit-task .title{
  font-size: 20px;
  font-family: 'Roboto',system,-apple-system,BlinkMacSystemFont,Helvetica Neue,Segoe UI,Arial,sans-serif;
  font-weight: 700;
  margin-bottom: 15px;
}


#tasks{
  display: grid;
  grid-gap: 20px;
  grid-template-columns: repeat(auto-fill,minmax(270px,1fr));
  position: relative;
  margin: 50px auto;
}

#tasks .task{
  position: relative;
  padding: 20px;
  background-color: #fff;
  border: 1px solid #dadce0;
  border-radius: 8px;
  cursor: default;
  -webkit-transition: all .2s ease-in-out;
  transition: all .2s ease-in-out;
  overflow-wrap: break-word;
  text-align: left;
}

#tasks .task:hover{
  -webkit-box-shadow: 0px 0px 4px 0px rgba(0,0,0,0.15);
  -moz-box-shadow: 0px 0px 4px 0px rgba(0,0,0,0.15);
  box-shadow: 0px 0px 4px 0px rgba(0,0,0,0.15);
}
#tasks .task .title{
  display: block;
  font-weight: 500;
  font-size: 16px;
}
#tasks .task .desc{
  display: block;
  font-size: 15px;
  white-space: pre-line;
}

#tasks .task .edit{
  position: absolute;
  top: 5px;
  right: 5px;
  font-size: 14px;
  color: #888888;
  cursor: pointer;
  transition: all 200ms;
}
#tasks .task .edit:hover{
  font-size: 16px;
  color: #494949;
}

#tasks .task .delete{
  position: absolute;
  top: 26px;
  right: 7px;
  font-size: 15px;
  color: #888888;
  cursor: pointer;
  transition: all 200ms;
}
#tasks .task .delete:hover{
  font-size: 16px;
  color: #e61e1e;
}

#edit-task .delete{
  position: absolute;
  bottom: 10px;
  left:15px;
  font-size: 20px;
  color: #888888;
  cursor: pointer;
  transition: all 200ms;
}
#edit-task .delete:hover{
  color: #e61e1e;
}
#edit-task .save{
  position: absolute;
  bottom: 10px;
  right: 25px;
  font-size: 15px;
  color: #000000;
  background: #ececec;
  padding: 6px 25px;
  font-weight: 500;
  border-radius: 5px;
  cursor: pointer;
  transition: all 200ms;
  -webkit-box-shadow: 0px 2px 5px 0px rgba(0,0,0,0.31);
  -moz-box-shadow: 0px 2px 5px 0px rgba(0,0,0,0.31);
  box-shadow: 0px 2px 5px 0px rgba(0,0,0,0.31);
}
#edit-task .save:hover{
  background-color: #9dffd3;
  padding: 6px 29px;
  right: 23px;
}
</style>
