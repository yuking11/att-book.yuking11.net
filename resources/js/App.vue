<template>
  <div>
    <Header />
    <Navbar />
    <main>
      <div class="l-container">
        <RouterView />
      </div>
    </main>
    <Footer />
  </div>
</template>

<script>
import { FORBIDDEN, NOT_FOUND, UNAUTHORIZED, INTERNAL_SERVER_ERROR } from './util'
import Header from './components/Header.vue'
import Navbar from './components/Navbar.vue'
import Footer from './components/Footer.vue'

export default {
  components: {
    Header,
    Navbar,
    Footer
  },
  computed: {
    errorCode () {
      return this.$store.state.error.code
    }
  },
  watch: {
    errorCode: {
      async handler (val) {
        if (val === INTERNAL_SERVER_ERROR) {
          this.$router.push('/500')
        } else if (val === FORBIDDEN) {
          this.$router.push('/403')
        } else if (val === UNAUTHORIZED) {
          await axios.get('/api/refresh-token')
          this.$store.commit('auth/setUser', null)
          this.$router.push('/login')
        } else if (val === NOT_FOUND) {
          this.$router.push('/not-found')
        }
      },
      immediate: true
    },
    $route () {
      this.$store.commit('error/setCode', null)
    }
  }
}
</script>
