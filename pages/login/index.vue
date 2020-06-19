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
                <v-toolbar-title>Login form</v-toolbar-title>
              </v-toolbar>
              <v-card-text>
                <v-form>
                  <v-text-field>
                    v-model="email"
                    label="Login"
                    name="login"
                    prepend-icon="person"
                    type="text"
                  </v-text-field>

                  <v-text-field>
                    v-model="password"
                    id="password"
                    label="Password"
                    name="password"
                    prepend-icon="lock"
                    type="password"
                    </v-text-field>
                </v-form>
              </v-card-text>
              <v-card-actions>
                <v-spacer />
                <v-btn color="primary" @click="handleLogin">Login</v-btn>
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
        snackbar: false,
        snackbarText: '',
        snackbarColor: ''
      }
    },
    methods: {

      async handleLogin() {
        try {
          let res = await this.$axios.post(env.axios.baseURL + 'auth/login', {
            email: this.email,
            password: this.password
          })
          if (res.success) {
            this.$router.push('/')
          }
        } catch (e) {
          console.log(e)
          this.snackbar = true
          this.snackbarText = e.response.data.message
          this.snackbarColor = 'red'
          console.log('EMail: ' + this.email)
        }
      }
    }
  }
</script>
