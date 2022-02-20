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
            <v-toolbar-title>List Users</v-toolbar-title>
            <v-spacer></v-spacer>
            <v-btn icon>
              <v-icon>mdi-magnify</v-icon>
            </v-btn>
          </v-app-bar>
          <v-container fluid>
            <v-row
              align="center"
              justify="end"
            >
              <v-btn
                depressed
                color="primary"
                to='/users/new'
                nuxt
              >
                Add User
              </v-btn>
            </v-row>
          </v-container>
          <v-container fluid>
            <v-simple-table dark>
              <template v-slot:default>
                <thead>
                  <tr>
                    <th class="text-left">
                      Id
                    </th>
                    <th class="text-left">
                      Name
                    </th>
                    <th class="text-left">
                      Email
                    </th>
                  </tr>
                </thead>
                <tbody>
                  <tr
                    v-for="user in users"
                    :key="user.id"
                  >
                    <td>
                      <nuxt-link :to='`/users/${user.id}`'>{{ user.id }}</nuxt-link>
                    </td>
                    <td>{{ user.name }}</td>
                    <td>{{ user.email }}</td>
                  </tr>
                </tbody>
              </template>
            </v-simple-table>
          </v-container>
        </v-card>
      </v-container>
    </v-main>
  </v-app>
</template>

<script lang="ts">
import { gql } from 'graphql-tag'

const USERS_QUERY = gql `
  query {
    users {
      nodes {
        id
        name
        email
      }
    }
  }
`;

type User = {
  id: Number,
  name: String,
  email: String
};

export default {
  data () {
    return {
      users: [] as Array<User>
    }
  },

  apollo: {
    users: {
      query: USERS_QUERY,
      fetchPolicy: 'no-cache',
      manual: true,
      result({ data } : { data:any }) {
        this.users = data.users.nodes
      }
    }
  }
};
</script>
