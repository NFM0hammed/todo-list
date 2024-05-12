<template>
  <div class="todo-list">
    <h2 class="title"><span>Todo List</span> App</h2>
    <div class="all-tasks-complete-tasks">
      <h4>
        Tasks<span class="all-tasks">{{ tasks.length }}</span>
      </h4>
      <h4>
        Completed<span class="complete-tasks">{{ tasksDone }}</span>
      </h4>
    </div>
    <!-- Add Task to the Page -->
    <div class="add-task">
      <input
        type="text"
        placeholder="Add Task"
        v-model="taskContent"
        autofocus
        @keydown.enter="addTask()"
        ref="input"
      />
      <div class="add-remove-all">
        <button
          @click="addTask()"
          :class="taskContent.length > 0 ? 'active' : ''"
        >
          Add
        </button>
        <button
          @click="removeAllTasksFromLocalStorage()"
          :class="tasks.length > 0 ? 'active' : ''"
        >
          Remove All
        </button>
      </div>
    </div>
    <!-- Show Message if there's no Task -->
    <div class="no-task" v-if="this.tasks.length < 1">There's no Task !</div>
    <!-- Show all Tasks on the Page -->
    <div class="task" v-for="(task, i) in tasks" :key="i" :id="task.id">
      <div
        class="the-task"
        :style="task.done ? 'text-decoration: line-through' : ''"
      >
        {{ task.theTask }}
      </div>
      <div class="handle-the-task">
        <button
          @click="removeSpecificTaskFromLocalStorage(task.id, task.done)"
          class="remove"
        >
          Remove
        </button>
        <button @click="completeTask(task, i)" class="done">Done</button>
      </div>
    </div>
    <footer>
      <p>Created by <a href="https://github.com/NFM0hammed">NFM0hammed</a></p>
    </footer>
  </div>
</template>

<script>
export default {
  name: "App",
  data: function () {
    return {
      tasks: [],
      tasksDone: 0,
      taskContent: "",
    };
  },
  created() {
    this.getTasksFromLocalStorage();
    this.numbersOfTasksDone;
  },
  methods: {
    addTask() {
      let count = 0;
      // Check if the element is an space
      this.taskContent.split("").forEach((ele) => {
        if (ele == " ") {
          count++;
        }
      });

      // Minimum 1 chars [ Not space ]
      if (this.taskContent != "" && count != this.taskContent.length) {
        this.tasks.push({
          id: Date.now(),
          theTask: this.taskContent,
          done: false,
        });
        this.addTaskTolocalStorage();
        this.taskContent = "";
        this.$refs.input.focus();
      }
    },
    addTaskTolocalStorage() {
      localStorage.setItem("data", JSON.stringify(this.tasks));
    },
    getTasksFromLocalStorage() {
      if (localStorage.getItem("data")) {
        this.tasks.push(...JSON.parse(localStorage.getItem("data")));
      }
    },
    removeAllTasksFromLocalStorage() {
      this.tasks = [];
      this.tasksDone = 0;
      localStorage.removeItem("data");
    },
    removeSpecificTaskFromLocalStorage(id, task) {
      if (task) {
        this.tasksDone--;
      }
      this.tasks = this.tasks.filter((ele) => ele.id != id);
      this.addTaskTolocalStorage();
      if (this.tasks.length === 0) {
        this.removeAllTasksFromLocalStorage();
      }
    },
    completeTask(task, i) {
      task.done = !task.done;
      this.tasks[i].done = task.done;
      this.addTaskTolocalStorage();
      task.done ? this.tasksDone++ : this.tasksDone--;
    },
  },
  computed: {
    numbersOfTasksDone() {
      this.tasks.forEach((ele) => {
        if (ele.done) {
          this.tasksDone++;
        }
      });
      return this.tasksDone;
    },
  },
};
</script>

<style lang="scss">
@import url("https://fonts.googleapis.com/css2?family=Poppins:ital,wght@0,100;0,200;0,300;0,400;0,500;0,600;0,700;0,800;0,900;1,100;1,200;1,300;1,400;1,500;1,600;1,700;1,800;1,900&display=swap");

/*
  Variables
*/
$mainColor: #f9f9f9;
$blueColor: #00b9ff;
$grayColor: #777;
$margin: 5px;
$borderBottom: 2px solid $blueColor;
$borderRadius: 5px;
$transition: 0.3s ease-in-out;
$mobileSize: "(max-width: 767px)";

body {
  background-color: $mainColor;
}

#app {
  font-family: "Poppins", sans-serif;
  -webkit-font-smoothing: antialiased;
  -moz-osx-font-smoothing: grayscale;
  text-align: center;
  margin-top: 60px;
}

.todo-list {
  .title {
    font: {
      weight: 500;
    }

    margin: {
      top: 50px;
      bottom: 50px;
    }

    span {
      position: relative;
      color: $blueColor;

      &::after {
        position: absolute;
        content: "";
        background-color: $blueColor;
        width: 100%;
        height: 3px;
        bottom: -5px;
        left: 0;
      }
    }
  }

  .all-tasks-complete-tasks {
    display: flex;
    justify-content: space-around;
    background-color: $mainColor;
    margin: {
      bottom: 20px;
    }
    border-bottom: $borderBottom;
    h4 {
      color: $grayColor;
      font: {
        weight: 600;
      }
      span {
        display: inline-block;
        color: #fff;
        width: 50px;
        margin-left: 10px;
        padding: 5px 0;
        border-radius: $borderRadius;
        &.all-tasks {
          background-color: $blueColor;
        }
        &.complete-tasks {
          background-color: #2bff00;
        }
      }
    }
  }

  background-color: #fff;
  max-width: 500px;
  margin: auto;
  border-radius: $borderRadius;
  padding: 20px;

  .add-task {
    display: flex;
    justify-content: space-between;
    align-items: center;

    input {
      flex: 1;
      background-color: $mainColor;
      color: $blueColor;

      font: {
        size: 18px;
      }

      padding: 5px;
      border: none;
      border-bottom: $borderBottom;

      margin: {
        right: calc(
          $margin + $margin
        ); // Margin right [Add button]: 5px + Margin left [Remove all button]: 5px = 10px
      }

      &:focus {
        outline: none;
      }

      @media #{$mobileSize} {
        & {
          margin: 0;
        }
      }
    }

    .add-remove-all {
      @media #{$mobileSize} {
        & {
          margin: {
            top: $margin;
          }
        }
      }

      button {
        color: #fff;
        font-weight: 700;
        width: 100px;
        height: 32px; // Same height of input field
        border: none;
        cursor: no-drop;

        &:first-of-type {
          background-color: #00b9ffb0;
          margin: {
            right: $margin;
          }

          &.active {
            background-color: $blueColor;
            cursor: pointer;

            &:hover {
              background-color: #00b9ffb0;
            }
          }
        }

        &:last-of-type {
          background-color: #cb0000b0;

          margin: {
            left: $margin;
          }

          &.active {
            background-color: #cb0000;
            cursor: pointer;

            &:hover {
              background-color: #cb0000b0;
            }
          }
        }

        padding: 0;
        transition: $transition;
      }
    }

    @media #{$mobileSize} {
      & {
        align-items: normal;
        flex-direction: column;
        text-align: left;
      }
    }
  }

  .no-task {
    background-color: $mainColor;
    color: $grayColor;
    margin: {
      top: 20px;
    }
    padding: 10px;
  }

  .task {
    display: flex;
    justify-content: space-around;
    background-color: $mainColor;
    margin: 20px 0;
    padding: 20px;
    border-radius: $borderRadius;

    .the-task {
      background-color: #fff;
      color: $grayColor;
      flex: 1;
      text-align: justify;
      text-wrap: balance;
      min-height: 65px; // Same height of two buttons
      margin: 0 20px 0 0;

      padding: {
        left: 10px;
        right: 10px;
      }
    }

    .handle-the-task {
      button {
        display: block;
        color: #fff;
        width: 100px;
        border: none;
        padding: 0;
        height: 30px;
        cursor: pointer;

        &:first-of-type {
          background-color: red;

          margin: {
            bottom: $margin;
          }

          &:hover {
            background-color: rgb(234, 107, 107);
          }
        }

        &:last-of-type {
          background-color: #00cf00;

          margin: {
            top: $margin;
          }

          &:hover {
            background-color: #9dd49d;
          }
        }

        transition: $transition;
      }
    }
  }
  footer p {
    color: $grayColor;
    text-align: left;
    font: {
      size: 14px;
      weight: 300;
    }
    margin-bottom: 0;
    a {
      color: #000;
      font: {
        weight: 600;
      }
    }
  }
}
</style>
