<template>
  <div id="app">
    <img alt="Vue logo" src="./assets/logo.png">
    <div class="container">
      <div class="row">
        <div class="col-md-4">
          <h2>NOTE LIST</h2>
          <div class="overflow">
            <ul class="list-group" v-for="note in notes" :key="note.id">
              <li class="list-group-item list-hover" @mouseenter="enter(note.id)" @mouseleave="leave(note.id)"
                @click.prevent="click(note.id)">
                <h6 class="font-weight-bold">{{ note.title }}</h6>
                <p class="wrap">{{ note.content }}</p>
                <button v-if="!front" @click.prevent="del(note.id)" class="btn btn-danger ml-1">Delete</button>
              </li>
            </ul>
          </div>
        </div>
        <div class="col-md-8">
          <form @submit.prevent="save()">
            <input type="hidden" v-model="form.id">
            <input type="text" v-model="form.title" class="form-control mb-1" :class="{danger : isDanger}"
              placeholder="Title">
            <textarea name="content" id="content" cols="80" rows="15" v-model="form.content" :class="{danger : isDangerr}" class="form-control"
              placeholder="Content"></textarea>
            <button v-if="!update" type="submit" class="btn btn-success ml-1 mt-1 float-right">Save</button>
          </form>
          <button v-if="update" @click.prevent="updatee()" class="btn btn-primary ml-1 mt-1 float-right">Update</button>
          <button @click="cancel()" class="btn btn-danger mt-1 float-right">Cancel</button>
        </div>
      </div>
    </div>
  </div>
</template>

<script>
  import axios from 'axios'

  export default {
    name: 'app',
    components: {

    },
    data() {
      return {
        notes: [],
        form: {
          id: '',
          title: '',
          content: ''
        },
        update: false,
        front: true,
        isDanger: false,
        isDangerr: false
      }
    },
    created() {
      this.load();
    },
    methods: {
      load() {
        axios.get("http://localhost:3000/notes").then(res => {
          this.notes = res.data
        })
      },
      click(id) {
        axios.get("http://localhost:3000/notes/" + id).then(res => {
          this.form.id = res.data.id
          this.form.title = res.data.title
          this.form.content = res.data.content
          this.update = true
        })
      },
      enter() {
        this.front = false
      },
      leave() {
        this.front = true
      },
      cancel() {
        this.form.id = ''
        this.form.title = ''
        this.form.content = ''
        this.update = false
      },
      save() {
        if (!this.form.title) {
          this.isDanger = true
        }else if(!this.form.content){
          this.isDangerr = true
        }
        else {
          axios.post("http://localhost:3000/notes/", {
            title: this.form.title,
            content: this.form.content
          }).then(() => {
            this.form.title = ''
            this.form.content = ''
            this.load()
            this.isDanger = false
            this.isDangerr = false
          })
        }
      },
      updatee() {
        axios.put("http://localhost:3000/notes/" + this.form.id, {
          title: this.form.title,
          content: this.form.content
        }).then(() => {
          this.form.id = ''
          this.form.title = ''
          this.form.content = ''
          this.update = false
          this.load()
        })
      },
      del(id) {
        axios.delete("http://localhost:3000/notes/" + id).then(() => {
          this.form.id = ''
          this.form.title = ''
          this.form.content = ''
          this.load()
        })
      }
    }
  }
</script>

<style>
  #app {
    font-family: 'Avenir', Helvetica, Arial, sans-serif;
    -webkit-font-smoothing: antialiased;
    -moz-osx-font-smoothing: grayscale;
    text-align: center;
    color: #2c3e50;
  }

  .overflow {
    overflow-y: auto;
    height: 370px;
  }

  li {
    cursor: pointer;
  }

  .wrap {
    text-overflow: ellipsis;
    overflow: hidden;
    white-space: nowrap;
  }

  .list-hover:hover {
    background-color: #41b783;
  }

  .danger {
    border-color: red;
  }
</style>