<template>
  <div class="wrapper-content wrapper-content--fixed">
    <section>
      <div class="container">

        <!-- search -->
        <search 
          :value="search"
          placeholder='Type username...'
          @search="search = $event"
        />
        <button 
          class="btn btnPrimary"
          @click="getRepos"
        >
          Search
        </button>

        <p v-if="error">
          {{ error }}
        </p>

        <!-- repos -->
        <div class="repos__wrapper" v-if="repos">
          <!-- repo item -->
          <h2>
            Repositories
          </h2>
          <a target="_blank" :href="repo.html_url" class="repo__item" v-for='repo in repos' :key='repo.id'>
            <div class="repo__info">
              <span class="link">{{repo.name}}</span>
              <span>
                {{ repo.stargazers_count }} ⭐
              </span>
            </div>
          </a>
        </div>
      </div>
    </section>
  </div>
</template>

<script>
// libs
import axios from 'axios';

// components
import search from '@/components/Search.vue';


export default {
  components: {
    search
  },
  data() {
    return {
      search: '',
      repos: null,
      users: null,
      error: null,
    }
  },
  methods: {
    // 
    getRepos () {
      axios
        .get(`https://api.github.com/users/${this.search}/repos`)
          .then(res => {
            console.log('repos: ' + res.data);
            this.repos = res.data;
            this.error = null
          })
          .catch(err => {
            this.error = 'Can not find this user'
            this.repos = null;
          });
      // axios
      //   .get(`https://api.github.com/users/${this.search}`)
      //     .then(res => {
      //       console.log('users: ' + res.data);
      //     })
      //     .catch(err => {
      //       this.error = 'Can not find this user'
      //       this.repos = null;
      //     })
    }
  }
}
</script>

<style lang="scss" scoped>
  .container {
    display: flex;
    align-items: center;
    flex-direction: column;
  }
  .btn {
    margin-top: 20px;
    transition: .4s ease-in;
    &:hover {
      opacity: .8;
    }
  }
 
  .repos__wrapper {
    width: 600px;
    margin: 30px 0;
  }
  h2 {
    margin-bottom: 30px;
  }
  .repo__item {
    margin-bottom: 15px;
    padding: 10px 5px;
    border: rosybrown solid 1px;
    display: block;
    transition: .2s ease-in-out;
    .link {
      border: none;
    }
    &:hover {
      background-color: rgba(#dbdbdb, .9);
      .link {
          color: rgb(238, 98, 98);
      }
    }
    .repo__info {
      display: flex;
      align-items: center;
      justify-content: space-between;
    }
  }
</style>