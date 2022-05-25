<template>
  <form @submit.prevent="getRepos" class="search-block">
    <Search
      @search="search = $event"
      :value="search"
      placeholder="Type user name..."
    />
    <button class="btn btnPrimary btn-search" type="submit">Search</button>
  </form>

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
      repos: null
    }
  },
  methods: {
    async getRepos() {
      // console.log(`get user ${this.search} repos`)
      await fetch(`https://api.github.com/users/${this.search}/repos`)
        .then(response => {
          // console.log(response.json())
          return response.json()
        })
        .then(data => {
          this.repos = data
        })
        .catch(error => {
          console.log(error)
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
</style>
