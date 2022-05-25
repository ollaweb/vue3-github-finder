<template>
  <div class="wrapper__search">
    <!-- Search -->
    <div class="search">
      <div class="search-icon">
        <svg
          xmlns="http://www.w3.org/2000/svg"
          width="24"
          height="24"
          viewBox="0 0 24 24"
          fill="none"
          stroke="currentColor"
          stroke-width="2"
          stroke-linecap="round"
          stroke-linejoin="round"
          class="feather feather-search"
        >
          <circle cx="11" cy="11" r="8"></circle>
          <line x1="21" y1="21" x2="16.65" y2="16.65"></line>
        </svg>
      </div>
    </div>
    <!-- Search input -->
    <input type="text" :placeholder="placeholder" v-model="search" />
  </div>
</template>

<script>
export default {
  props: {
    value: {
      type: String,
      required: true
    },
    placeholder: {
      type: String,
      default: 'Search'
    }
  },
  data() {
    return { search: this.value }
  },
  watch: {
    search(val) {
      // console.log(val)
      this.$emit('search', val)
    }
  }
}
</script>

<style lang="scss">
/* Search */
.wrapper__search {
  margin: 50px 0 30px;
  position: relative;
  display: flex;
  justify-content: space-between;
  align-items: center;
  & svg {
    color: #e4e4e4;
  }
  & input {
    margin: 0 auto;
    padding: 16px 16px 16px 56px !important;
    margin-bottom: 0px;
    border-radius: 30px;
    color: #909399;
    font-size: 16px;

    height: auto;
  }
}
.search {
  top: 0px;
  bottom: 0px;
  left: 0px;
  position: absolute;
}
.search-icon {
  height: 100%;
  padding-left: 16px;
  padding-right: 16px;
  align-items: center;
  display: flex;
}

.notes-show {
  text-align: right;
  flex: 0 1 20%;
  & svg {
    color: #999;
    cursor: pointer;
    &.active {
      color: #0e39c9;
    }
    &:not(:last-child) {
      margin-right: 10px;
    }
  }
}
</style>
