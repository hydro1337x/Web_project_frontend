<template>
  <v-app>
    <v-content>
      <v-app-bar
        color="primary"
        dark
      >
        <v-app-bar-nav-icon @click="drawer = true"></v-app-bar-nav-icon>

        <v-toolbar-title>Account</v-toolbar-title>
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
            <v-list-item @click="$router.push('/')">
              <v-list-item-icon>
                <v-icon>mdi-home</v-icon>
              </v-list-item-icon>
              <v-list-item-title>Home</v-list-item-title>
            </v-list-item>

            <v-list-item @click="drawer=false">
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
                <v-toolbar-title>User info</v-toolbar-title>
              </v-toolbar>
              <v-card-text>
                <v-form>
                  <v-text-field v-model="email"
                                label="Email"
                                name="email"
                                type="text"
                                disabled>

                  </v-text-field>

                  <v-text-field v-model="firstName"
                                id="firstName"
                                label="First name"
                                name="firstName"
                                type="text">

                  </v-text-field>

                  <v-text-field v-model="lastName"
                                id="lastName"
                                label="Last name"
                                name="lastName"
                                type="text">

                  </v-text-field>

                  <v-btn @click="$router.push('/account/password')">Change password</v-btn>
                </v-form>
              </v-card-text>
              <v-card-actions>
                <v-spacer />
                <v-btn color="primary" @click="handleInfoUpdate">Update</v-btn>
              </v-card-actions>
            </v-card>
          </v-col>
        </v-row>
      </v-container>
    </v-content>

    <v-snackbar
      v-model="snackbar"
      :color="snackbarColor"
      :timeout="5000"
      :top="true"
      class="snackbar"
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
  </v-app>
</template>
<script>
  import env from "~/env"

  export default {

    middleware: 'auth',

    data() {
      return {
        email: this.$auth.user.email,
        firstName: this.$auth.user.firstName,
        lastName: this.$auth.user.lastName,
        drawer: false,
        snackbar: false,
        snackbarText: '',
        snackbarColor: ''
      }
    },

    methods: {
      async handleInfoUpdate() {
        try {
          let response = await this.$axios.$patch(env.axios.baseURL + 'auth/user/info/update', {
            firstName: this.firstName,
            lastName: this.lastName
          })
          if (response.success) {
            this.snackbar = true
            this.snackbarText = response.message
            this.snackbarColor = 'primary'
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

<style>
  .snackbar {
    margin-top: 100px;
  }
</style>
