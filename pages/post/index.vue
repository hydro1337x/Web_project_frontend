<template>
  <v-app>
    <v-content>
      <v-app-bar
        color="primary"
        dark
      >
        <v-app-bar-nav-icon @click="drawer = true"></v-app-bar-nav-icon>

        <v-toolbar-title v-if="mode === 'create'">Create post</v-toolbar-title>
        <v-toolbar-title v-else>Edit post</v-toolbar-title>
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
            <template>

              <v-list-item @click="$router.push('/')">
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

              <v-list-item @click="drawer = false">
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

          </v-list-item-group>
        </v-list>
      </v-navigation-drawer>

      <v-container
        class="fill-height"
        fluid
      >
        <v-row
          align="center"
          justify="center"
        >
          <v-col
            cols="12"
            sm="8"
            md="4"
          >
            <v-card class="elevation-12">
              <v-toolbar
                color="primary"
                dark
                flat
              >
                <v-toolbar-title>Post</v-toolbar-title>
              </v-toolbar>
              <v-card-text>
                <v-form>
                  <v-text-field v-model="title"
                                id="title"
                                label="Title"
                                name="title"
                                type="text">

                  </v-text-field>

                  <v-textarea v-model="description"
                                id="description"
                                label="Description"
                                name="description"
                                type="text">

                  </v-textarea>
                </v-form>
              </v-card-text>
              <v-card-actions>
                <v-spacer />
                <v-btn v-if="mode === 'create'" color="primary" @click="handleCreatePost">Create</v-btn>
                <v-btn v-else color="primary" @click="handleEditPost">Edit</v-btn>
              </v-card-actions>
            </v-card>
          </v-col>
        </v-row>
      </v-container>

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

      <v-row justify="center">
        <v-dialog v-model="dialog" persistent max-width="290">
          <v-card>
            <v-card-title class="headline">Alert</v-card-title>
            <v-card-text v-if="mode === 'create'">Post successfully created</v-card-text>
            <v-card-text v-else>Post successfully edited</v-card-text>
            <v-card-actions>
              <v-spacer></v-spacer>
              <v-btn color="primary" text @click="$router.push('/')">Okay</v-btn>
            </v-card-actions>
          </v-card>
        </v-dialog>
      </v-row>
    </v-content>
  </v-app>
</template>
<script>

  import env from "~/env"

  export default {

    middleware: 'auth',

    mounted () {
      this.mode = this.$route.query.mode
      this.postId = this.$route.query.id

      if (this.mode === 'edit') {
        this.getPost()
      }
    },

    data() {
      return {
        postId: '',
        mode: '',
        title: '',
        description: '',
        drawer: false,
        dialog: false,
        snackbar: false,
        snackbarText: '',
        snackbarColor: ''
      }
    },

    methods: {
      async handleCreatePost() {
        try {
          let response = await this.$axios.$post(env.axios.baseURL + 'post/create', {
            title: this.title,
            description: this.description
          })
          if (response.success) {
            this.dialog = true
          }
        } catch (e) {
          console.log(e)
          this.snackbar = true
          this.snackbarText = e.response.data.message
          this.snackbarColor = 'red'
        }
      },

      async handleEditPost() {
        try {
          let response = await this.$axios.$patch(env.axios.baseURL + 'post/edit', {
            id: this.postId,
            title: this.title,
            description: this.description
          })
          if (response.success) {
            this.dialog = true
          }
        } catch (e) {
          console.log(e)
          this.snackbar = true
          this.snackbarText = e.response.data.message
          this.snackbarColor = 'red'
        }
      },

      async getPost() {
        try {
          let response = await this.$axios.$get(env.axios.baseURL + 'post/get', {
            params: {
              id: this.postId
            }
          })
          if (response.success) {
            this.title = response.data.title
            this.description = response.data.description
          }
        } catch (e) {
          console.log(e)
          this.snackbar = true
          this.snackbarText = e.response.data.message
          this.snackbarColor = 'red'
        }
      },

      async handleLogout() {
        await this.$auth.logout()
        this.$router.push('/login')
      }
    }
  }
</script>
