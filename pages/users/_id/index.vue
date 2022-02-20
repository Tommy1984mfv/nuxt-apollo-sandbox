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
            <v-toolbar-title>User Detail</v-toolbar-title>
            <v-spacer></v-spacer>
            <v-btn icon>
              <v-icon>mdi-magnify</v-icon>
            </v-btn>
          </v-app-bar>
          <v-container fluid>
            <v-row>
              <v-col>ID</v-col>
              <v-col>{{ user.id }}</v-col>
            </v-row>
            <v-row>
              <v-col>Name</v-col>
              <v-col>{{ user.name }}</v-col>
            </v-row>
            <v-row>
              <v-col>Email</v-col>
              <v-col>{{ user.email }}</v-col>
            </v-row>
          </v-container>
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
              <nuxt-link
                :to='`/users/${id}/edit`'
              >
                <v-btn
                  depressed
                  color="primary"
                >
                  Edit
                </v-btn>
              </nuxt-link>
              <v-btn
                depressed
                color="error"
                @click.native='deleteUser'
              >
                Delete
              </v-btn>
            </v-row>
          </v-container>
        </v-card>
      </v-container>
    </v-main>
  </v-app>
</template>

<script lang='ts'>
import { gql } from 'graphql-tag'

const USER_QUERY = gql `
  query($id: ID!) {
    user(id: $id) {
      id
      name
      email
    }
  }
`;

const DELETE_USER_MUTATION = gql `
  mutation($input: DeleteUserInput!) {
    deleteUser(input: $input) {
      clientMutationId
    }
  }
`;

type User = {
  id: String,
  name: String,
  email: String
};

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
        email: '',
      } as User
    }
  },

  methods: {
    deleteUser() {
      this.$apollo.mutate({
        mutation: DELETE_USER_MUTATION,
        variables: {
          input: { id: this.id }
        }
      }).then(({ data }) => {
        alert('Delete user successful!')
        this.$router.push('/users')
      }).catch(({ graphQLErrors }) => {
        alert(graphQLErrors.map((e: { message: string }) => e.message).join(', '))
      })
    },
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
  }
};
</script>
