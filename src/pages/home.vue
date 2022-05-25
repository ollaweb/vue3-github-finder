<template>
  <form @submit.prevent="getRepos" class="search-block">
    <Search
      @search="search = $event"
      :value="search"
      placeholder="Type user name..."
    />
    <button
      v-if="!error && repos"
      class="btn btnPrimary btn-search"
      type="submit"
    >
      Search again
    </button>
    <button v-else class="btn btnPrimary btn-search" type="submit">
      Search
    </button>
  </form>
  <div class="error" v-if="error">
    <p>{{ error }}</p>
  </div>
  <div v-if="user" class="user">
    <h2 class="repo-title">User:</h2>
    <div class="user__wrapper">
      <img class="user__ava" :src="user.avatar_url" :alt="user.name" />
      <div class="user__info">
        <div class="user__name">
          <span>Name: </span>
          <p>{{ user.name }}</p>
        </div>
        <div class="user__repo">
          <span>Public repos: </span>
          <p>{{ user.public_repos }}</p>
        </div>
      </div>
    </div>
  </div>
  <ul class="repo" v-if="repos">
    <h2 class="repo-title">Repos:</h2>
    <li v-for="repo in repos" :key="repo.id" class="repo__item">
      <div class="repo__info">
        <a class="link" target="_blank" :href="repo.html_url"
          >{{ repo.name }}
        </a>

        <span>{{ repo.stargazers_count }} ⭐</span>
      </div>
    </li>
  </ul>
</template>

<script>
import Search from '@/components/UI/Search.vue'

export default {
  components: {
    Search
  },
  data() {
    return {
      search: '',
      error: null,
      user: null,
      repos: null
    }
  },
  methods: {
    // async getUser() {

    // },
    async getRepos() {
      await fetch(`https://api.github.com/users/${this.search}`)
        .then(response => {
          if (!response.ok) {
            throw new Error("Can't find this user")
          } else {
            return response.json()
          }
        })
        .then(data => {
          this.user = data
          this.error = null
        })
        .catch(error => {
          this.user = null
          this.error = error.message
        })

      await fetch(`https://api.github.com/users/${this.search}/repos`)
        .then(response => {
          if (!response.ok) {
            throw new Error("Can't find this user")
          } else {
            return response.json()
          }
        })
        .then(data => {
          this.repos = data
          this.error = null
        })
        .catch(error => {
          this.repos = null
          this.error = error.message
        })
    }
  }
}
</script>

<style lang="scss">
.search-block {
  margin: 0 auto;
  width: 100%;
  max-width: 500px;
  display: flex;
  flex-direction: column;
}
.btn-search {
  margin: 0 auto;
  width: 50%;
}
.repo-title {
  margin: 20px auto 10px;
  width: 100%;
  max-width: 500px;
  font-weight: 700;
}
.user {
  margin: 0px auto 30px;
  width: 100%;
  max-width: 500px;
  &__wrapper {
    margin-top: 20px;
    display: flex;
  }
  &__ava {
    flex: 0 1 200px;
    margin-right: 20px;
    max-width: 180px;
    border-radius: 100%;
    & img {
      width: 100%;
      height: 100px;
      object-fit: cover;
    }
  }
  &__info {
    flex: 0 1 300px;
    & > * {
      display: flex;
      align-items: center;
      justify-content: space-between;
      border-bottom: 1px solid #dbdbdb;
      &:not(:last-child) {
        margin-bottom: 15px;
      }
    }
  }
}
.repo {
  margin: 0px auto 30px;
  width: 100%;
  max-width: 500px;
  &__item {
    padding: 3px 0;
  }
  &__info {
    display: flex;
    align-items: center;
    justify-content: space-between;
    border-bottom: 1px solid #dbdbdb;
  }
}
.error {
  margin: 20px auto 0;
  width: 100%;
  max-width: 500px;
  text-align: center;
  color: red;
}
</style>
