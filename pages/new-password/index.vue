<template>
  <v-app id="inspire">
    <v-content>
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
                <v-toolbar-title>New password</v-toolbar-title>
              </v-toolbar>
              <v-card-text>
                <v-form>
                  <v-text-field v-model="newPassword"
                                label="New password"
                                name="newPassword"
                                type="password">

                  </v-text-field>

                  <v-text-field v-model="repeatedNewPassword"
                                label="Repeated new password"
                                name="repeatedNewPassword"
                                type="password">

                  </v-text-field>

                </v-form>
              </v-card-text>
              <v-card-actions>
                <v-spacer />
                <v-btn color="primary" @click="createNewPassword">Submit</v-btn>
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
          <template v-slot:activator="{ on, attrs }">
            <v-btn
              color="primary"
              dark
              v-bind="attrs"
              v-on="on"
            >
              Open Dialog
            </v-btn>
          </template>
          <v-card>
            <v-card-title class="headline">Alert</v-card-title>
            <v-card-text>New password successfully created</v-card-text>
            <v-card-actions>
              <v-spacer></v-spacer>
              <v-btn color="primary" text @click="$router.push('/login')">Okay</v-btn>
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
    props: {
      source: String,
    },
    data() {
      return {
        newPassword: '',
        repeatedNewPassword: '',
        dialog: false,
        snackbar: false,
        snackbarText: '',
        snackbarColor: ''
      }
    },
    methods: {

      async createNewPassword() {
        try {
          let response = await this.$axios.$patch(env.axios.baseURL + 'auth/password/new', {
            accessToken: this.$route.query.token,
            newPassword: this.newPassword,
            repeatedNewPassword: this.repeatedNewPassword
          })
          console.log(response)
          if (response.success) {
            this.dialog = true
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
