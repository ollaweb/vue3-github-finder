<template>
  <form @submit.prevent="getUserInfo" class="search-block">
    <Search
      @search="search = $event"
      :value="search"
      placeholder="Type user name..."
    />
    <button
      v-if="!error && user"
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
    <div class="repo-titles">
      <div @click="sort('name')" class="repo-titles__item">
        <h2 class="repo-title">Repos</h2>
        <img src="@/assets/img/arrow-down.svg" alt="arrow down" />
      </div>
      <div @click="sort('stargazers_count')" class="repo-titles__item">
        <h2 class="repo-title">Stars</h2>
        <img src="@/assets/img/arrow-down.svg" alt="arrow down" />
      </div>
    </div>
    <li v-for="repo in reposSort" :key="repo.id" class="repo__item">
      <div class="repo__info">
        <a class="link" target="_blank" :href="repo.html_url"
          >{{ repo.name }}
        </a>

        <span>{{ repo.stargazers_count }} ⭐</span>
      </div>
    </li>
  </ul>
  <button
    v-if="repos && page.current < Math.ceil(repos.length / page.length)"
    @click="showMore"
    class="btn btnPrimary btn-show-more"
    type="button"
  >
    Show more
  </button>
  <button
    v-if="repos && page.current === Math.ceil(repos.length / page.length)"
    @click="showLess"
    class="btn btnPrimary btn-show-less"
    type="button"
  >
    Show less
  </button>
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
      repos: null,
      currentSort: 'name',
      currentSortDir: 'asc',
      page: {
        current: 1,
        length: 5
      }
    }
  },
  computed: {
    reposSort() {
      return this.repos
        .filter((row, index) => {
          let end = this.page.current * this.page.length
          if (index >= 0 && index < end) return true
        })
        .sort((a, b) => {
          let mod = 1
          if (this.currentSortDir === 'desc') mod = -1
          if (a[this.currentSort] < b[this.currentSort]) return -1 * mod
          if (a[this.currentSort] > b[this.currentSort]) return 1 * mod
          return 0
        })
    }
  },
  methods: {
    async getUserInfo() {
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
    },
    sort(e) {
      if (e === this.currentSort) {
        this.currentSortDir = this.currentSortDir === 'asc' ? 'desc' : 'asc'
      } else {
        this.currentSort = e
        this.currentSortDir = 'asc'
      }
    },
    showMore() {
      if (this.page.current * this.page.length < this.repos.length)
        this.page.current += 1
    },
    showLess() {
      this.page.current = 1
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
.btn-show-more {
  margin: 0 auto;
}
.btn.btnPrimary {
  &.btn-show-less {
    margin: 0 auto;
    border: none;
    background-color: rgba(73, 76, 232, 0.5);
  }
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
.repo-titles {
  margin: 10px 0 20px;
  width: 100%;
  max-width: 500px;
  display: flex;
  align-items: center;
  justify-content: space-between;
  &__item {
    display: flex;
    cursor: pointer;
  }
}
.repo-title {
  margin-right: 10px;
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
.error {
  margin: 20px auto 0;
  width: 100%;
  max-width: 500px;
  text-align: center;
  color: red;
}
</style>
