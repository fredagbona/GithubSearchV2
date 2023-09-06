<template>

  <button class="btn btn-primary mt-5 mx-auto d-block" data-bs-toggle="modal" data-bs-target="#howitworks">
    How it works
  </button>
    <div class="container-fluid mt-5">
      <div class="row">
        <div class="col-md-3">

        </div>
        <div class="col-10 col-md-6">
          <input v-model="username" class="form-control"  @keyup.enter="getUsers" placeholder="Search for a user">
        </div>
        <div class="col-2 col-md-2">
          <button v-if="loading === false" @click="getUsers" class="btn btn-success "><i class="bi bi-search"></i></button>
          <button v-else class="btn btn-secondary"  disabled>
            <span class="spinner-border spinner-border-sm" aria-hidden="true"></span>
            <span role="status">Loading...</span>
          </button>
        </div>
      </div>
    </div>

  <div class="container">
    <div v-if="users !== ''" class="row">
      <div :key="user.id" v-for="user in users"  class="col-xs-12 col-md-4">
        <div class="card mb-3 mt-2">
          <div class="card-body">
            <img :src="user.avatar_url" class="rounded-circle mx-auto d-block" width="150" alt="User Avatar">
            <h5 class="card-title text-center mt-3">{{ user.login }}</h5>
            <p  class="card-text text-center"><a :href="user.html_url" target="_blank">{{ user.html_url }}</a> </p>
            <button class="btn btn-success mx-auto d-block" data-bs-toggle="modal" data-bs-target="#userInfos" @click="openModal(user.login)" >
              View More infos
            </button>
          </div>
        </div>
      </div>
    </div>

    <div v-if="onneUser !== ''" class="row">
      <div class="col-xs-12 col-md-6 mx-auto d-block">
        <div class="card mb-3 mt-2  ">
          <div class="card-body">
            <img :src="onneUser.avatar_url" class="rounded-circle mx-auto d-block" width="150" alt="User Avatar">
            <h5 class="card-title text-center mt-3">{{ onneUser.login }}</h5>
            <p v-if="onneUser.name !== null" class="card-text">Full Name : {{ onneUser.name }}</p>
            <p class="card-text">Number of public repository : {{ onneUser.public_repos }}</p>
            <p class="card-text">Followers : {{ onneUser.followers }}</p>
            <p v-if="onneUser.blog !== ''" class="card-text">Website : <a :href="onneUser.blog" target="_blank">{{ onneUser.blog }}</a> </p>
            <p  class="card-text">Github link : <a :href="onneUser.html_url" target="_blank">{{ onneUser.html_url }}</a> </p>
          </div>
        </div>
      </div>
    </div>

    <div v-if="userError">
      <h1 class="text-danger text-center">No user found</h1>
    </div>
  </div>

  <div class="modal fade" id="userInfos" tabindex="-1" aria-labelledby="exampleModalLabel" aria-hidden="true">
    <div class="modal-dialog modal-dialog-centered">
      <div class="modal-content">
        <div class="modal-header">
          <h1 class="modal-title fs-5" id="exampleModalLabel">{{ modalInfo.login }} Informations </h1>
          <button type="button" class="btn-close" data-bs-dismiss="modal" aria-label="Close"></button>
        </div>
        <div class="modal-body">
          <img :src="modalInfo.avatar_url" class="rounded-circle mx-auto d-block" width="150" alt="User Avatar">
          <p v-if="modalInfo.name !== null" class="card-text mt-5">Full Name : {{ modalInfo.name }}</p>
          <p class="card-text">Number of public repository : {{ modalInfo.public_repos }}</p>
          <p class="card-text">Followers : {{ modalInfo.followers }}</p>
          <p v-if="modalInfo.blog !== ''" class="card-text">Website : <a :href="modalInfo.blog" target="_blank">{{ modalInfo.blog }}</a> </p>
          <p  class="card-text">Github link : <a :href="modalInfo.html_url" target="_blank">{{ modalInfo.html_url }}</a> </p>
        </div>
        <div class="modal-footer">
          <button type="button" class="btn btn-danger" data-bs-dismiss="modal">Close</button>
        </div>
      </div>
    </div>
  </div>

  <!-- How it works Section -->
  <div class="modal fade" id="howitworks" tabindex="-1" aria-labelledby="exampleModalLabel" aria-hidden="true">
    <div class="modal-dialog modal-dialog-centered">
      <div class="modal-content">
        <div class="modal-header">
          <h1 class="modal-title fs-5" id="exampleModalLabel">How it works</h1>
          <button type="button" class="btn-close" data-bs-dismiss="modal" aria-label="Close"></button>
        </div>
        <div class="modal-body">
          <ul>
            When you enter a username in search bar

            <li>If this user exists, the result will be a user card with user's informations. </li>
            <li>If user don't exists, the result wil be a error message.</li>
            <li>If you click search button within empty search bar, the result will be a list of 40 defaults users on Github with informations of each of them !</li>
          </ul>
        </div>
        <div class="modal-footer">
          <button type="button" class="btn btn-success" data-bs-dismiss="modal">I understand</button>
        </div>
      </div>
    </div>
  </div>

</template>

<script style>
    import axios from 'axios';

    export default  {
        name: 'Home',
      data() {
        return{
          onneUser: '',
          users: null,
          username: '',
          modalInfo: '',
          userError: false,
          loading: false
        }
      },
      methods: {
        getUsers() {
          this.loading = true
          setTimeout(() => {
            const searched = this.username;
            if(searched === '') {
              this.onneUser = '';
              getAllUsers()
                  .then((response) => {
                    this.users = response
                  })
                  .catch((error) => {
                    console.log(error)
                  })
            } else  {
              this.users = '';
              getUser(searched)
                  .then((response) => {
                    console.log(response)
                    this.userError = false
                    this.onneUser = response
                  })
                  .catch((error) => {
                    console.log(error)
                    this.onneUser = ''
                    this.userError = true
                  })
            }

            this.loading = false
          }, 5000)


        },

        openModal(user){
          getUser(user)
              .then((response) => {
                console.log(response)
                this.modalInfo = response
              })
              .catch((error) => {
                console.log(error)
              })
        }
      }
    }

    function getUser(username) {
      return axios
          .get(`https://api.github.com/users/${username}`)
          .then((response) => {
            console.log(response.data)
            return response.data;
          })
         .catch((error) => {
           console.log(error)
           throw error
         })
    }

    function getAllUsers(){
     return axios
          .get("https://api.github.com/users?per_page=40&page=1")
          .then((response) => {
            console.log(response.data)
            return response.data
          })
         .catch((error) => {
           console.log(error)
           throw error
         })

    }




</script>

<style scoped>

</style>