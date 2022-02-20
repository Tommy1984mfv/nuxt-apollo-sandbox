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
            <v-toolbar-title>Edit User</v-toolbar-title>
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
                      v-model="user.id"
                      label="ID"
                      outlined
                      disabled
                    ></v-text-field>
                  </v-col>
                </v-row>
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

type User = {
  id: String,
  name: String,
  email: String
};

const USER_QUERY = gql `
  query($id: ID!) {
    user(id: $id) {
      id
      name
      email
    }
  }
`;

const UPDATE_USER_MUTATION = gql `
  mutation($input: UpdateUserInput!) {
    updateUser(input: $input) {
      user {
        id
        name
        email
      }
    }
  }
`;

export default {
  asyncData({ params }) {
    return {
      id: params.id,
    }
  },

  data () {
    return {
      id: '',
      user: {
        id: '',
        name: '',
        email: ''
      } as User
    }
  },

  methods: {
    save() {
      this.$apollo.mutate({
        mutation: UPDATE_USER_MUTATION,
        variables: {
          input: { user: this.user }
        }
      }).then(({ data }) => {
        alert('Update user successful!')
        this.$router.push(`/users/${this.id}`)
      }).catch(({ graphQLErrors }) => {
        alert(graphQLErrors.map((e: { message: string }) => e.message).join(', '))
      })
    }
  },

  apollo: {
    users: {
      query: USER_QUERY,
      fetchPolicy: 'no-cache',
      manual: true,
      variables() {
        return {
          id: this.id
        }
      },
      result({ data } : { data:any }) {
        delete data.user.__typename
        this.user = data.user
      }
    }
  },
}
</script>
