<template>
  <div id="app">
    <h1 @click="info = true">CLICK FOR INFORMATIONS</h1>

    <draggable v-model="lists" group="lists" class="board" handle=".list__name">
      <div class="listWrapper" v-for="list in lists" :key="list.id">
        <div class="list">
          <div class="list__name" v-if="!list.editingList" @click="editList(list)">{{list.name}}</div>
          <div class="list__edit" v-else>
            <input type="text" v-model="list.name">
            <div class="list__edit__buttons">
              <button @click="saveEditList(list)">SAVE</button>
              <button @click="cancelEditList(list)">CANCEL</button>
              <button @click="deleteList(list)">DELETE</button>
            </div>
          </div>
          <draggable group="tasks" class="taskWrapper" handle=".task__title">
            <div v-for="(task) in list.tasks" :key="task.id" class="task">
              <div class="task__labels">
                <span
                  class="label"
                  v-for="label in task.labels"
                  :key="label.name"
                  v-bind:style="{ background: label.color}"
                ></span>
              </div>
              <div class="task__title">{{task.name}}</div>
              <button @click="task.options = true" class="optionsMenu">...</button>

              <div class="task__users">
                <span class="user user__addNew">+</span>
                <span
                  class="user"
                  v-for="user in task.users"
                  :key="user.name"
                  v-bind:style="{ background: user.color + '40', color: user.color}"
                >{{user.initial}}</span>
              </div>
              <div stop class="task__options" v-if="task.options">
                <button @click="task.options = false" class="task__options__close">CLOSE</button>
                <div>
                  <input type="text" v-model="labelColor" placeholder="#ffffff">
                  <button class="addLabel" @click="addLabel(task)">Add Label</button>
                </div>
                <button class="deleteTask" @click="deleteTask(list, task)">DELETE TASK</button>
              </div>
            </div>
          </draggable>
          <div class="addTask">
            <div
              v-if="!list.addingTask"
              @click="list.addingTask = !list.addingTask"
              class="addTask__add"
            >Add card</div>
            <div v-else>
              <input
                type="text"
                placeholder="Add new task"
                v-model="newTaskName"
                @keyup.enter="addTask(list, list.id)"
                @blur="cancelTaskAdd(list)"
                v-focus
              >
            </div>
          </div>
        </div>
      </div>
      <div class="listWrapper">
        <div class="addList">
          <input
            type="text"
            placeholder="Add new list"
            v-model="newListName"
            @keyup.enter="addList()"
          >
        </div>
      </div>
    </draggable>
    <div v-bind:class="[info ? infoClass : '', modalInformations]">
      <button @click="info = false">CLOSE</button>
      <ul>
        <li>Click on list title to edit it.</li>
        <li>Click on "..." button on card to open card options. You can delete task here, or add colored label - u have to enter color in HEX like #ffffff (extra service, there isnt in u task list for me).</li>
        <li>For drag task, catch title of the task, for drag list catch title of the list.</li>
        <li>"+" icon for new users doesnt work, i haven't more time to do it.</li>
        <li>Click enter to add new list and task.</li>
      </ul>
    </div>
  </div>
</template>

<script>
import HelloWorld from "./components/HelloWorld";
import draggable from "vuedraggable";

export default {
  name: "App",
  components: {
    HelloWorld,
    draggable
  },
  data() {
    return {
      info: false,
      infoClass: "active",
      modalInformations: "modalInformations",
      newListName: "",
      newTaskName: "",
      labelColor: "",
      newListId: 3,
      newTaskId: 3,
      editedList: [],
      lists: [
        {
          id: 1,
          name: "lista 1",
          editingList: false,
          addingTask: false,
          tasks: [
            {
              id: 1,
              name: "task 1",
              options: false,
              users: [
                {
                  name: "Kamil",
                  initial: "K",
                  color: "#F7657C"
                },
                {
                  name: "Adam",
                  initial: "A",
                  color: "#4381FF"
                }
              ],
              labels: [
                {
                  id: 1,
                  color: "#F7657C"
                },
                {
                  id: 2,
                  color: "#4381FF"
                }
              ]
            }
          ]
        },
        {
          id: 2,
          name: "lista 2",
          editingList: false,
          addingTask: false,
          tasks: [
            {
              id: 2,
              name: "task 2",
              options: false,
              users: [
                {
                  name: "Paul",
                  initial: "P",
                  color: "#fa6b4c"
                }
              ],
              labels: [
                {
                  id: 1,
                  color: "#fa6b4c"
                }
              ]
            }
          ]
        }
      ]
    };
  },
  methods: {
    editList(list) {
      this.editedList = list;
      this.beforeEdit = list.name;
      list.editingList = true;
    },
    saveEditList(list) {
      list.editingList = false;
      this.editedList = null;
    },
    cancelEditList(list) {
      list.name = this.beforeEdit;
      this.editedList = null;
      list.editingList = false;
    },
    deleteList(list) {
      console.log(list.name);
      let index = this.lists.indexOf(list);
      this.lists.splice(index, 1);
    },
    deleteTask(list, task) {
      let index = list.tasks.indexOf(task);
      list.tasks.splice(index, 1);
      console.log(task.name);
    },
    addList() {
      if (!this.newListName) {
        return;
      }
      console.log(this.newListName);
      this.lists.push({
        id: this.newListId,
        name: this.newListName,
        tasks: [],
        editingList: false,
        addingTask: false
      });
      this.newListId++;
      this.newListName = "";
    },
    addTask(list, id) {
      if (!this.newTaskName) {
        return;
      }
      console.log(this.newTaskName);
      console.log(list.id);
      list.tasks.push({
        id: this.newTaskId,
        name: this.newTaskName,
        options: false,
        labels: [],
        users: []
      });
      this.newTaskId++;
      this.newTaskName = "";
      list.addingTask = false;
    },
    cancelTaskAdd(list) {
      list.addingTask = false;
      this.newTaskName = "";
    },
    addLabel(task) {
      if (!this.labelColor) {
        return;
      }
      task.labels.push({
        id: 3,
        color: this.labelColor
      });
      this.labelColor = "";
    }
  },
  directives: {
    focus: {
      // directive definition
      inserted: function(el) {
        el.focus();
      }
    }
  }
};
</script>


<style lang="scss">
#app {
  font-family: "Avenir", Helvetica, Arial, sans-serif;
  color: #2c3e50;
}
h1 {
  cursor: pointer;
}
.modalInformations {
  position: absolute;
  top: 0;
  left: 0;
  right: 0;
  bottom: 0;
  z-index: 123;
  background: #ccc;
  color: #fff;
  display: none;
  text-align: center;
  padding: 50px;
  ul {
    max-width: 500px;
    margin: 0 auto;
    font-size: 25px;
    text-align: left;
  }
}
.active {
  display: block;
}
.board {
  overflow-x: scroll;
  overflow-y: hidden;
  white-space: nowrap;
  position: absolute;
  top: 60px;
  bottom: 0;
  left: 0;
  right: 0;
  background: #f6f7fb;
  padding: 10px;
}
.listWrapper {
  display: inline-block;
  height: 100%;
  width: 260px;
  margin: 20px;
  box-sizing: border-box;
  vertical-align: top;
  white-space: nowrap;
}
.list,
.addList {
  padding: 16px;
  max-height: 95%;
  background: #fff;
  box-shadow: 0px 0px 4px 0px rgba(0, 0, 0, 0.31);
  display: flex;
  flex-direction: column;
  white-space: pre-line;
}
.list {
  &__name {
    font-size: 28px;
    margin-top: 26px;
    margin-bottom: 27px;
  }
  &__edit {
    margin-top: 25px;
    input {
      font-size: 25px;
      max-width: 100%;
    }
    &__buttons {
      margin-top: 4px;
    }
  }
  .addTask {
    text-align: center;
    margin-top: 10px;
    padding: 0 3px;
    &__add {
      cursor: pointer;
    }
    input {
      width: 100%;
    }
  }
  .taskWrapper {
    min-height: 100px;
    overflow: auto;
    .task {
      border: 1px solid #ccc;
      box-shadow: 0px 0px 4px 0px rgba(0, 0, 0, 0.31);
      margin: 4px 2px;
      padding: 6px 12px;
      position: relative;
      min-height: 90px;
      .optionsMenu {
        position: absolute;
        top: 8px;
        right: 8px;
        width: 24px;
        height: 24px;
        display: inline-block;
        border-radius: 4px;

        border: 1px solid #ccc;
        text-align: center;
        line-height: 24px;
        font-weight: 400;
        cursor: pointer;
      }
      &__name {
        font-size: 20px;
      }
      &__labels {
        width: 100%;
        min-height: 18px;
        .label {
          width: 40px;
          height: 8px;
          display: inline-block;
          border-radius: 4px;
          margin-right: 4px;
        }
      }
      &__users {
        position: absolute;
        bottom: 8px;
        right: 8px;
        left: 0;
        text-align: right;

        .user {
          width: 24px;
          height: 24px;
          display: inline-block;
          border-radius: 4px;
          margin-left: 4px;
          border: 1px solid #ccc;
          text-align: center;
          line-height: 24px;
          font-weight: 400;
          cursor: pointer;
          &__addNew {
            border-style: dotted;
          }
        }
      }
      &__options {
        position: absolute;
        top: 0;
        right: 0;
        background: #cccccca8;
        padding: 40px 20px 10px 20px;
        width: 100%;
        overflow: hidden;
        box-sizing: border-box;
        display: flex;
        justify-content: center;
        align-items: center;
        flex-direction: column;
        input {
          width: 30%;
        }
        .deleteTask {
          margin-top: 10px;
        }
        &__close {
          position: absolute;
          top: 8px;
          right: 8px;
        }
      }
    }
    .sortable-chosen {
      background: #ccc;
    }
    .sortable-ghost {
      background: #ccc;
    }
  }
}
</style>
