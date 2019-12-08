<template>
  <v-app app>
    <v-navigation-drawer
            v-model="drawer"
            app
            mobile-break-point="650"
            clipped
    >
      <v-list subheader>
        <v-subheader>Users in Room</v-subheader>
        <v-list-item v-for="(item,i) in users" :key="`item_${i}`">
          <v-list-item-content>
            <v-list-item-title>{{item.name}}</v-list-item-title >
          </v-list-item-content>
          <v-list-item-icon>
            <v-icon :color="item.id === user.id ? 'primary':'grey'">mdi-chat</v-icon>
          </v-list-item-icon>
        </v-list-item>
      </v-list>
    </v-navigation-drawer>

    <v-app-bar
            app
            clipped-left
    >
      <v-app-bar-nav-icon @click.stop="drawer = !drawer" />
      <v-toolbar-title>Room {{this.user.room}}</v-toolbar-title>
      <v-spacer></v-spacer>
      <v-btn icon @click="exit"><v-icon>mdi-home</v-icon></v-btn>
    </v-app-bar>

    <v-content>
      <div style="height: 100%">
        <nuxt/>
      </div>
    </v-content>
  </v-app>
</template>

<script>
  import {mapState , mapMutations} from 'vuex'
  export default {
    props: {
      source: String,
    },
    data: () => ({
      drawer: false
    }),
    computed : {
      ...mapState(['user' , 'users'])
    },
    methods : {
      ...mapMutations(['clearData']),
      exit () {
        this.$socket.emit('userLeft' , this.user.id , () => {
          this.clearData();
          this.$router.push('/?message=leftChat');
        })

      }
    },
    created () {
      this.$vuetify.theme.dark = true
    },
  }
</script>
