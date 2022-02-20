<template>
  <v-app>
    <v-main>
      <v-container>
        <v-card
          class="mx-auto"
          outlined
        >
          <v-app-bar
            dark
            color="pink"
          >
            <v-app-bar-nav-icon></v-app-bar-nav-icon>
            <v-toolbar-title>Create User</v-toolbar-title>
            <v-spacer></v-spacer>
            <v-btn icon>
              <v-icon>mdi-magnify</v-icon>
            </v-btn>
          </v-app-bar>
          <v-container fluid>
            <v-row
              align="center"
              justify="space-around"
            >
              <v-btn
                depressed
                color="secondary"
                to='/users'
                nuxt
              >
                List Users
              </v-btn>
              <v-btn
                depressed
                color="primary"
                @click='save'
              >
                Save
              </v-btn>
            </v-row>
          </v-container>
          <v-container fluid>
            <v-form>
              <v-container>
                <v-row>
                  <v-col>
                    <v-text-field
                      v-model="user.name"
                      label="Name"
                      outlined
                    ></v-text-field>
                  </v-col>
                </v-row>
                <v-row>
                  <v-col>
                    <v-text-field
                      v-model="user.email"
                      label="Email"
                      outlined
                    ></v-text-field>
                  </v-col>
                </v-row>
              </v-container>
            </v-form>
          </v-container>
        </v-card>
      </v-container>
    </v-main>
  </v-app>
</template>

<script lang='ts'>
import { gql } from 'graphql-tag'

const CREATE_USER_MUTATION = gql `
  mutation($input: CreateUserInput!) {
    createUser(input: $input) {
      user {
        id
        name
        email
      }
    }
  }
`;

type User = {
  name: String,
  email: String
};

export default {
  data () {
    return {
      user: {
        name: '',
        email: '',
      } as User
    }
  },
  methods: {
    save() {
      this.$apollo.mutate({
        mutation: CREATE_USER_MUTATION,
        variables: {
          input: { user: this.user }
        }
      }).then(({ data }) => {
        alert('Create user successful!')
        this.$router.push('/users')
      }).catch(({ graphQLErrors }) => {
        alert(graphQLErrors.map((e: { message: string }) => e.message).join(', '))
      })
    }
  },
};
</script>
