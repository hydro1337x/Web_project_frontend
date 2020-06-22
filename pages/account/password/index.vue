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
                <v-toolbar-title>Password</v-toolbar-title>
              </v-toolbar>
              <v-card-text>
                <v-form>
                  <v-text-field v-model="oldPassword"
                                label="Old password"
                                name="oldPassword"
                                type="password">

                  </v-text-field>

                  <v-text-field v-model="newPassword"
                                id="newPassword"
                                label="New password"
                                name="newPassword"
                                type="password">

                  </v-text-field>

                  <v-text-field v-model="repeatedNewPassword"
                                id="repeatedNewPassword"
                                label="Repeated new password"
                                name="repeatedNewPassword"
                                type="password">

                  </v-text-field>
                </v-form>
              </v-card-text>
              <v-card-actions>
                <v-spacer />
                <v-btn color="primary" @click="handlePasswordUpdate">Update</v-btn>
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
        oldPassword: '',
        newPassword: '',
        repeatedNewPassword: '',
        drawer: false,
        snackbar: false,
        snackbarText: '',
        snackbarColor: ''
      }
    },

    methods: {
      async handlePasswordUpdate() {
        try {
          let response = await this.$axios.$patch(env.axios.baseURL + 'auth/user/password/update', {
            oldPassword: this.oldPassword,
            newPassword: this.newPassword,
            repeatedNewPassword: this.repeatedNewPassword
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
