<template>
  <div>
    <div>
      <div class="form-group">
        <input
          class="form-control bb-input"
          type="text"
          placeholder="Note something.."
          name="todoInput"
          v-model="todoInput"
        />
      </div>
      <div class="form-group">
        <button class="form-control" id="todo-btn" @click="addToDo()">
          <span v-if="editing" title="Save" class="ml-2">Save</span>
          <span title="Add" v-else class="ml-2">Add</span>
        </button>
      </div>
    </div>

    <div>
      <h6 class="mb-3 alt-title">To Do's:</h6>

      <ul class="to-do-list-ul">
        <li v-for="todo in todolists.body" :key="todo.id">
          <span :title="todo.name" class="todo-name">{{ todo.name }}</span>
          <div class="d-flex">
            <button
              title="Edit"
              class="alt-btn d-flex justify-content-center align-items-center"
              @click="edit(todo.id, todo.name)"
            >
              <svg
                xmlns="http://www.w3.org/2000/svg"
                width="15"
                height="15"
                viewBox="0 0 14.959 14.92"
              >
                <path
                  id="pencil"
                  d="M14.86,2.993c-.8-.821-1.619-1.69-2.417-2.488A1.008,1.008,0,0,0,11.762.2a.827.827,0,0,0-.634.235L1.671,9.8a.122.122,0,0,0-.047.094L.216,14.258l-.023.07a.61.61,0,0,0,.094.563h0a.724.724,0,0,0,.774.188L5.4,13.671a.178.178,0,0,0,.094-.047L14.86,4.26a.816.816,0,0,0,.258-.634A.879.879,0,0,0,14.86,2.993ZM4.769,12.521,1.718,13.53,2.7,10.479,9.439,3.885l1.995,1.995ZM12.3,4.988,10.307,2.993l1.361-1.361c.681.681,1.361,1.361,1.995,2.065Z"
                  transform="translate(-0.159 -0.2)"
                  fill="#222327"
                />
              </svg>
            </button>
            <button
              @click="deleteTodo(todo.id)"
              title="Delete"
              class="alt-btn d-flex justify-content-center align-items-center"
            >
              <svg
                xmlns="http://www.w3.org/2000/svg"
                width="15"
                height="14.922"
                viewBox="0 0 15 14.922"
              >
                <path
                  id="close"
                  d="M16.475,15.419l6.18-6.18A.747.747,0,0,0,21.6,8.183l-6.18,6.18L9.208,8.214a.68.68,0,0,0-1.025,0,.74.74,0,0,0,0,1.056l6.18,6.149L8.183,21.6a.715.715,0,0,0,0,1.056.81.81,0,0,0,.528.217.681.681,0,0,0,.528-.217l6.211-6.18,6.211,6.149a.964.964,0,0,0,.528.217.779.779,0,0,0,.528-.217.715.715,0,0,0,0-1.056Z"
                  transform="translate(-7.95 -7.95)"
                  fill="#222327"
                />
              </svg>
            </button>
          </div>
        </li>
      </ul>
    </div>
  </div>
</template>
<script>
import axios from "axios";
export default {
  data() {
    return {
      token:
        "eyJhbGciOiJIUzI1NiIsInR5cCI6IkpXVCJ9.eyJ1c2VybmFtZSI6IkJlcmtlIEJhdHVyIiwiaWQiOjUxLCJpYXQiOjE2MTQ4NzA1MDgsImV4cCI6MTYxNDk0MjUwOH0.0LwtEaP29Eq69VpocJcM6lprRNO14YgOF70kBU-V85E",
      todolists: {},
      todoInput: null,
      editing: false,
      editingId: 0
    };
  },
  methods: {
    getTodoList() {
      axios
        .get("https://aodapi.eralpsoftware.net/todo", {
          headers: {
            token: this.token,
          },
        })
        .then((response) => (this.todolists = response.data));
    },
    deleteTodo(id) {
      axios.delete("https://aodapi.eralpsoftware.net/todo/" + id, {
        headers: {
          token: this.token,
        },
      });

      this.getTodoList();
    },
    addToDo() {
      if (this.todoInput !== null) {
        if (this.editing) {
          axios.put(
            "https://aodapi.eralpsoftware.net/todo/" + this.editingId,
            {
              name: this.todoInput,
            },
            {
              headers: {
                token: this.token,
              },
            }
          );
          this.todoInput = "";
          this.editing = false;
          this.getTodoList();
        } else {
          axios.post(
            "https://aodapi.eralpsoftware.net/todo",
            {
              name: this.todoInput,
            },
            {
              headers: {
                token: this.token,
              },
            }
          );
          this.getTodoList();
        }
      }
    },
    edit(id, name) {
      this.todoInput = name;
      this.editing = true;
      this.editingId = id;
    },
  },
  mounted: function () {
    this.getTodoList();
  },
};
</script>


<style lang="scss">
.bb-input {
  border-radius: 0px;
  border: 2px solid #fff;
  background: transparent;
  color: #fff !important;
  &:focus {
    border: 2px solid #ffcb04;
    background: transparent;
  }
}
#todo-btn {
  cursor: pointer;

  color: #222327;
  font-weight: 600;
  border-radius: 0px;
  background: transparent;
  position: relative;
  transition: 600ms all;
  overflow: hidden;
  border: 2px solid white;
  &:hover {
    color: #fff;
    &::after {
      bottom: -100%;
    }
  }

  &::after {
    content: "";
    background-color: #fff;
    width: 100%;
    height: 100%;
    transition: 300ms all;
    position: absolute;
    left: 0;
    bottom: 0;
    z-index: -1;
  }
}

.to-do-list-ul {
  list-style-type: none;
  padding: 0px;
  margin: 0px;

  .done {
    span {
      &:after {
        content: "";
        position: absolute;
        left: 0;
        top: 0;
        width: 100%;
        height: 3px;
        background-color: #5cb85c;
      }
      text-decoration: line-through;
    }
  }

  .do {
    span {
      &:after {
        content: "";
        position: absolute;
        left: 0;
        top: 0;
        width: 100%;
        height: 3px;
        background-color: #cf2c04;
      }
    }
  }

  li {
    display: flex;
    justify-content: space-between;
    align-items: center;
    position: relative;
    padding: 6px 8px;
    font-weight: 500;
    background-color: darken(#fff, 6%);
    margin-bottom: 10px;
    &:nth-last-child(1) {
      margin-bottom: 0px;
    }
    &:hover {
      background-color: darken(#fff, 10%);
    }
  }
}

.todo-name {
  width: 65%;
  overflow: hidden; /* taşanları gizle */
  white-space: nowrap; /* alt satıra hiç inme */
  text-overflow: ellipsis;
}

.alt-btn {
  background: transparent;
  border: none;
  cursor: pointer;
}

.alt-title {
  color: #fff;
}
</style>