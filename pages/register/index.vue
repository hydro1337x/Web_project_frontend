<template>
  <v-app id="inspire">
    <v-content>
      <v-tabs
        fixed-tabs
        background-color="primary"
        dark
      >
        <v-tab to="/login">
          Login
        </v-tab>

        <v-tab to="/register">
          Register
        </v-tab>
      </v-tabs>
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
                <v-toolbar-title>Registration form</v-toolbar-title>
              </v-toolbar>
              <v-card-text>
                <v-form>
                  <v-text-field v-model="email"
                                label="Email"
                                name="email"
                                type="text">

                  </v-text-field>

                  <v-text-field v-model="password"
                                id="password"
                                label="Password"
                                name="password"
                                type="password">

                  </v-text-field>

                  <v-text-field v-model="repeatedPassword"
                                id="repeatPassword"
                                label="Repeat password"
                                name="repeatPassword"
                                type="password">

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
                </v-form>
              </v-card-text>
              <v-card-actions>
                <v-spacer />
                <v-btn color="primary" @click="handleRegister">Register</v-btn>
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
        email: '',
        password: '',
        repeatedPassword: '',
        firstName: '',
        lastName: '',
        snackbar: false,
        snackbarText: '',
        snackbarColor: ''
      }
    },
    methods: {

      async handleRegister() {
        try {
          let res = await this.$axios.$post(env.axios.baseURL + 'auth/register', {
            email: this.email,
            password: this.password,
            repeatedPassword: this.repeatedPassword,
            firstName: this.firstName,
            lastName: this.lastName
          })
          if (res.success) {
            this.$router.push('/')
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
