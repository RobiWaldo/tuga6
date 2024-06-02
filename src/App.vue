<template>
  <div class="background">

  </div>
  <div id="app" class="bg-navy text-white">
    <div class="container q-pa-md">
      <div class="border rounded p-4 mb-4" style="border-radius: 20px;">
        <form @submit.prevent="savePost">
          <div class="mb-3">
            <label for="title" class="form-label">Judul</label>
            <input type="text" class="form-control" id="title" v-model="newPost.title" required>
          </div>
          <div class="mb-3">
            <label for="content" class="form-label">Konten</label>
            <textarea class="form-control" id="content" v-model="newPost.content" required></textarea>
          </div>
          <button type="submit" class="btn btn-primary">Save</button>
        </form>
      </div>
      <div class="row">
        <div v-for="post in posts" :key="post.id" class="col">
          <div class="card h-100 w-500">
            <div class="card-body d-flex flex-column">
              <h5 class="card-title">{{ post.title }}</h5>
              <p class="card-text flex-grow-1">{{ post.content }}</p>
              <div class="d-flex justify-content-between">
                <button class="btn btn-warning" @click="editPost(post)">Edit</button>
                <button class="btn btn-danger" @click="deletePost(post.id)">Delete</button>
              </div>
            </div>
          </div>
        </div>
      </div>
    </div>
  </div>
</template>

<script>
import axios from 'axios';
import Swal from 'sweetalert2';

export default {
  data() {
    return {
      newPost: {
        title: '',
        content: ''
      },
      posts: [],
      apiUrl: 'http://localhost:3000/posts'
    };
  },
  methods: {
    async fetchPosts() {
      try {
        const response = await axios.get(this.apiUrl);
        this.posts = response.data;
      } catch (error) {
        console.error(error);
      }
    },
    async savePost() {
      try {
        await axios.post(this.apiUrl, this.newPost);
        this.newPost = { title: '', content: '' };
        this.fetchPosts();
      } catch (error) {
        console.error(error);
      }
    },
    async deletePost(id) {
      try {
        await axios.delete(`${this.apiUrl}/${id}`);
        this.fetchPosts();
      } catch (error) {
        console.error(error);
      }
    },
    async editPost(post) {
      const { value: formValues } = await Swal.fire({
        title: 'Edit Post',
        html:
          `<input id="swal-input1" class="swal2-input" placeholder="Title" value="${post.title}">` +
          `<textarea id="swal-input2" class="swal2-input" placeholder="Content">${post.content}</textarea>`,
        focusConfirm: false,
        preConfirm: () => {
          return {
            title: document.getElementById('swal-input1').value,
            content: document.getElementById('swal-input2').value
          };
        }
      });

      if (formValues) {
        try {
          await axios.put(`${this.apiUrl}/${post.id}`, formValues);
          this.fetchPosts();
        } catch (error) {
          console.error(error);
        }
      }
    }
  },
  mounted() {
    this.fetchPosts();
  }
}
</script>

<style>
@import 'bootstrap/dist/css/bootstrap.min.css';

#app {
  z-index: 1;
  color: white;
  min-height: 100vh;
  padding: 20px;
}
.container {
  max-width: 100vh;
  margin: auto;
}
.form-label, .form-control, .swal2-input {
  color: black;
}
.btn-primary {
  background-color: #007bff;
  border-color: #007bff;
}
.btn-warning {
  background-color: #ffc107;
  border-color: #ffc107;
}
.btn-danger {
  background-color: #dc3545;
  border-color: #dc3545;
}

.mb-3 label{
  color: whitesmoke;
}

.card, .mt-3 {
  display: grid;

}
.background{
  background-color: navy;
  position: fixed;
  top: 0;
  left: 0;
  min-height: 90%;
  min-width: 1500px;
  width: 100%;
  height: 100%;
  overflow: hidden;
  z-index: -1;
  object-fit: cover;
  -webkit-background-size:cover; -moz-background-size:cover; -o-background-size:cover; background-size: cover;
  filter: brightness(0.8);
}
</style>
