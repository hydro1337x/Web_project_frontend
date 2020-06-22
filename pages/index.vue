<template>
  <v-app>
    <v-content>
      <v-app-bar
        color="primary"
        dark
      >
        <v-app-bar-nav-icon @click="drawer = true"></v-app-bar-nav-icon>

        <v-toolbar-title>Posts</v-toolbar-title>
      </v-app-bar>

      <v-navigation-drawer
        v-model="drawer"
        absolute
        temporary
      >
        <v-list
          nav
          dense
        >
          <v-list-item-group
            active-class="primary--text"
          >
            <template v-if="$auth.loggedIn">

              <v-list-item @click="drawer=false">
                <v-list-item-icon>
                  <v-icon>mdi-home</v-icon>
                </v-list-item-icon>
                <v-list-item-title>Home</v-list-item-title>
              </v-list-item>

              <v-list-item @click="$router.push('/account')">
                <v-list-item-icon>
                  <v-icon>mdi-account</v-icon>
                </v-list-item-icon>
                <v-list-item-title>Account</v-list-item-title>
              </v-list-item>

              <v-list-item @click="$router.push('post?mode=create')">
                <v-list-item-icon>
                  <v-icon>mdi-pencil-box-outline</v-icon>
                </v-list-item-icon>
                <v-list-item-title>Create post</v-list-item-title>
              </v-list-item>

              <v-list-item color="error" @click="handleLogout">
                <v-list-item-icon>
                  <v-icon color="error">mdi-logout</v-icon>
                </v-list-item-icon>
                <v-list-item-title>Logout</v-list-item-title>
              </v-list-item>
            </template>

            <template v-else>

              <v-list-item @click="$router.push('/login')">
                <v-list-item-icon>
                  <v-icon>mdi-login</v-icon>
                </v-list-item-icon>
                <v-list-item-title>Login</v-list-item-title>
              </v-list-item>
            </template>

          </v-list-item-group>
        </v-list>
      </v-navigation-drawer>

      <v-snackbar
        v-model="snackbar"
        :color="snackbarColor"
        :timeout="5000"
        :top="true"
      >
        {{ snackbarText }}
        <v-btn
          dark
          text
          @click="snackbar = false"
        >
          Close
        </v-btn>
      </v-snackbar>

      <v-container id="container" v-for="(post, index) in posts" v-bind:key="post.user_id">
        <v-card
          class="mx-auto"
          max-width="500"
          outlined
        >

          <v-list-item three-line>
            <v-list-item-content>
              <v-list-item-title class="headline mb-1">{{post.title}}</v-list-item-title>
              <div class="d-flex align-center justify-space-between">
                <v-list-item-subtitle class="text-left">{{'created at: ' + post.created_at}}</v-list-item-subtitle>
                <v-list-item-subtitle class="text-right">{{'updated at: ' + post.updated_at}}</v-list-item-subtitle>
              </div>

              <v-list-item-subtitle class="subtitle-1 mb-2">{{post.description}}</v-list-item-subtitle>
            </v-list-item-content>
          </v-list-item>

          <v-card-actions v-if="$auth.loggedIn && post.user_id === $auth.user.id">
            <v-btn text @click="$router.push(`/post?mode=edit&id=${post.id}`)">Edit</v-btn>
            <v-btn text @click="handleDeletePost(index)">Delete</v-btn>
          </v-card-actions>
        </v-card>
      </v-container>

    </v-content>
  </v-app>
</template>
<script>

  import env from "~/env"

  export default {

    mounted () {
      this.fetchPosts()
    },

    data() {
      return {
        posts: [],
        drawer: false,
        snackbar: false,
        snackbarText: '',
        snackbarColor: ''
      }
    },

    methods: {
      async handleLogout() {
        await this.$auth.logout()
        this.$router.push('/login')
      },

      async fetchPosts() {
        try {
          let response = await this.$axios.$get(env.axios.baseURL + 'post/get')
          if (response.success) {
            console.log(response)
            this.posts = response.data
          }
        } catch (e) {
          console.log(e)
          this.snackbar = true
          this.snackbarText = e.response.data.message
          this.snackbarColor = 'red'
        }
      },

      async handleDeletePost(index) {
        console.log('ID: ', this.posts[index].id)
        try {
          let response = await this.$axios.$delete(env.axios.baseURL + 'post/delete', {
            data: { id: this.posts[index].id }
          })
          if (response.success) {
            this.posts.splice(index, 1)
          }
        } catch (e) {
          console.log(e)
          this.snackbar = true
          this.snackbarText = e.response.data.message
          this.snackbarColor = 'red'
        }
      }
    }
  }
</script>

<style>
  #id {
    margin-top: 50px;
  }
</style>
