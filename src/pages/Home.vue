<template>
  <div class="wrapper-content wrapper-content--fixed">
    <section>
      <div class="search__container">
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
      </div>
      <div class="container">

        <!-- search -->


    

        
        <div v-if="user" class="user">
          <div class="left">
            <img :src="user.avatar_url" alt="avatar">
          </div>
          <div class="right">
              <div class="user__info">
                <a class="link" :href="user.html_url"> {{ user.login }} </a>
                <span>Name: {{ user.name }} </span>
                <span v-if="user.location">Location: {{ user.location }} </span>
                <span v-if="user.email">Email: {{ user.email }} </span>
                <span> 
                  <a class="link" :href="user.followers_url" target="_blank">Followers</a>: {{ user.followers }} 
                  | 
                  <a class="link" :href="user.following_url" target="_blank">Following</a>: {{ user.following }} 
                </span>
                <span>Repositories: {{ user.public_repos }} </span>
                <span v-if="user.company">Company: {{ user.company }} </span>
                <span>Created at: {{ user.created_at.substr(0, user.created_at.length - 10) }} </span>
              </div>
          </div>
        </div>
        <!-- repos -->
        <div class="repos__wrapper" v-if="reposSort">
          <!-- repo item -->
          <h2>
            Repositories
          </h2>
          <p class="sort__debag">
            Debag:
            <br />
            sort: <span class="debagBlue">{{ currentSort }}</span
            >,
            <br />
            dir: <span class="debagBlue">{{ currentSortDir }}</span>
          </p>
          <div class="sort">
            <div class="sort__item" @click="sort('name')">
              Name <span v-if="currentSort === 'name'" :class="{active: currentSortDir === 'desc' && currentSort === 'name'}">	&#8595;</span>
            </div>
            <div class="sort__item" @click="sort('stargazers_count')">
              Stars <span v-if="currentSort === 'stargazers_count'" :class="{active: currentSortDir === 'desc' && currentSort === 'stargazers_count'}">	&#8595;</span>
            </div>
          </div>
          <a target="_blank" :href="repo.html_url" class="repo__item" v-for='repo in reposSort' :key='repo.id'>
            <div class="repo__info">
              <span class="link">{{repo.name}}</span>
              <span>
                {{ repo.stargazers_count }} &#9734;
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
      reposSort: null,
      user: null,
      error: null,
      currentSort: null,
      currentSortDir: "asc",
    }
  },
  methods: {
    // 
    getRepos () {
      axios
        .get(`https://api.github.com/users/${this.search}/repos`)
          .then(res => {
            this.repos = res.data;
            this.error = null
            this.sort();
          })
          .catch(err => {
            this.error = 'Can not find this user'
            this.repos = null;
          });
      axios
        .get(`https://api.github.com/users/${this.search}`)
          .then(res => {
            this.user = res.data;
          })
          .catch(err => {
            this.error = 'Can not find this user'
            this.repos = null;
          })
    },
    sort(sort) {
          if (sort === this.currentSort) {
              this.switchSortDirection();
          } else {
              this.currentSort = sort;
          }
          this.sortUsers();
      },
      switchSortDirection() {
          this.currentSortDir = this.currentSortDir === "asc" ? "desc" : "asc";
      },
      sortUsers() {
          this.reposSort = this.repos.sort((a, b) => {
              let mod = this.currentSortDir === "asc" ? 1 : -1;
              if (a[this.currentSort] < b[this.currentSort]) return -1 * mod;
              if (a[this.currentSort] > b[this.currentSort]) return mod;
              
              return 0;
          })
      },
  }
}
</script>

<style lang="scss" scoped>
  section {
    display: flex;
    flex-direction: column;
    align-items: center;
  }
  .search__container {
    width: 600px;
    display: flex;
    flex-direction: column;
    align-items: center;
  }
  .container {
    display: flex;
    align-items: start;
    justify-content: space-between;
    flex-direction: row-reverse;
  }
  .btn {
    margin-top: 20px;
    transition: .2s ease-in;
    &:hover {
      background-color: salmon;
      border: 1px solid salmon;
    }
  }
  .link {
    color: blue;
    border-bottom: none;
    transition: .2s ease-in-out;
    &:hover {
      color: salmon;
    }
  }
  .user {
    display: flex;
    align-items: start;
    justify-content: space-between;
    margin: 30px auto;
    width: 500px;
    position: sticky;
    left: 0;
    top: 100px;
    border: solid 1px #dbdbdb;
    padding: 20px 10px;
    &__info {
      display: flex;
      flex-direction: column;
      align-items: start;
      span {
        margin-top: 10px;
      }
    }
    .left {
      width: 40%;
      display: flex;
      align-items: start;
      justify-content: center;
      img {
        height: 150px;
        width: 150px;
        object-fit: contain;
        border-radius: 100%;
      }
    }
    .right {
      width: 60%;
      display: flex;
      align-items: start;
      justify-content: center;
    }
  }
  .repos__wrapper {
    width: 600px;
    margin: 30px 0;
  }
  h2 {
    margin-bottom: 30px;
  }
  .sort {
    width: 600px;
    display: flex;
    justify-content: space-between;
    align-items: center;
    margin: 10px 0;
    &__item {
      cursor: pointer;
      font-size: 20px;
      transition: .2s ease-in-out;
      display: flex;
      span {
        transition: .2s ease-in-out;
        display: block;
        margin-left: 2px;
        &.active {
          transform: rotate(-180deg);
        }
      }
      &:hover {
        color: blue;
      }
    }
  }
  .repo__item {
    margin-bottom: 15px;
    padding: 10px 5px;
    border: #dbdbdb solid 1px;
    display: block;
    transition: .2s ease-in-out;
    color: #636363;
    .link {
      border: none;
      color: #636363;
    }
    &:hover {
      background-color: rgba(#dbdbdb, .9);
      color: blue;
      .link {
          color: blue;
      }
    }
    .repo__info {
      display: flex;
      align-items: center;
      justify-content: space-between;
    }
  }
</style>