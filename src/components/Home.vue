<template>
  <main>
    <div class="" @click="loadUsers()">
      my filter
    </div>
    <div>
      categories: {{users}}
    </div>
    <section v-if="posts.length">
      <post-item
        v-for="post in posts"
        :key="post.id"
        :post="post"
      />
      <pagination
        v-if="totalPages > 1"
        :total="totalPages"
        :current="page"
      />
    </section>
  </main>
</template>

<script>
import PostItem from '@/components/template-parts/PostItem'
import Pagination from '@/components/template-parts/Pagination'

export default {
  name: 'Home',
  components: {
    PostItem,
    Pagination
  },
  props: {
    page: {
      type: Number,
      required: true
    }
  },
  data() {
    return {
      request: {
        type: 'posts',
        params: {
          per_page: this.$store.state.site.posts_per_page,
          page: this.page
        },
        showLoading: true
      },
      totalPages: 0,

      categoriesRequest: {
        type: 'categories',
        params: {
          per_page: 30
        }
      },

      usersRequest: {
        type: 'users',
        params: {
          per_page: 2
        }
      }
    }
  },

  computed: {
    posts() {
      return this.$store.getters.requestedItems(this.request)
    },

    categories () {
      return this.$store.getters.requestedItems(this.categoriesRequest)
    },

    users () {
      return this.$store.getters.requestedItems(this.usersRequest)
    }
  },

  methods: {
    getPosts(request) {
      return this.$store.dispatch('getItems', request)
    },
    setTotalPages() {
      this.totalPages = this.$store.getters.totalPages(this.request)
    },

    reload () {
      this.request.params.per_page = 10
      this.getPosts(this.request).then(() => this.setTotalPages())
    },

    loadCategories () {
      this.getPosts(this.categoriesRequest)
    },

    loadUsers () {
      this.getPosts(this.usersRequest)
    }
  },

  created() {
    this.getPosts(this.request).then(() => this.setTotalPages())
  }
}
</script>
