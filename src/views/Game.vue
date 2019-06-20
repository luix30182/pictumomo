<template>
  <v-app>
    <Menu/>
    <div class="border-wrap">
      <DrawBoard/>
      <div class="separator"></div>
      <v-container fluid>
        <v-layout row wrap fluid>
          <v-flex xs12>
            <v-list>
              <v-list-group :prepend-icon="listUsers.action" no-action>
                <template v-slot:activator>
                  <v-list-tile>
                    <v-list-tile-content>
                      <v-list-tile-title>{{ listUsers.title }}</v-list-tile-title>
                    </v-list-tile-content>
                  </v-list-tile>
                </template>
                <v-list-tile v-for="user in listUsers.users" :key="user.name">
                  <v-list-tile-content>
                    <v-list-tile-title>{{ user.name }}</v-list-tile-title>
                  </v-list-tile-content>
                </v-list-tile>
              </v-list-group>
            </v-list>
          </v-flex>
          <v-flex xs12>
            <h3>Hello! {{username}}</h3>
          </v-flex>
          <v-flex xs12 v-for="(message,i) in messages" :key="i">
            <Message :message="message"/>
          </v-flex>
          <!-- send mesage -->
          <v-bottom-nav>
          <v-flex xs12>
            <v-text-field v-model="message" outline clearable label="Message" type="text">
              <template v-slot:append>
                <v-fade-transition leave-absolute>
                  <v-progress-circular v-if="loading" size="24" color="info" indeterminate></v-progress-circular>
                  <img
                    @click="clickMe"
                    v-else
                    width="24"
                    height="24"
                    src="https://cdn.vuetifyjs.com/images/logos/v-alt.svg"
                    alt
                  >
                </v-fade-transition>
              </template>
            </v-text-field>
          </v-flex>
          </v-bottom-nav>
        </v-layout>
      </v-container>
    </div>
  </v-app>
</template>

<style scoped>
canvas {
  width: 100%;
}
body {
  touch-action: none;
}
.separator {
  height: 3px;
  width: 100%;
  background: linear-gradient(blue, cyan);
}
.separator-y {
  display: flex;
  height: 100%;
  width: 3px;
  background: linear-gradient(blue, cyan);
}
</style>

<script>
// @ is an alias to /src
import Menu from "@/components/Menu";
import Message from "@/components/Message";
import io from "socket.io-client";
import router from '../router';
import DrawBoard from '@/components/DrawBoard'

export default {
  name: "Game",
  components: {
    Menu,
    Message,
    DrawBoard
  },
  data() {
    return {
      username: "",
      message: "",
      loading: false,
      socket: io("localhost:3000"),
      // socket: io("https://pictumomo-chat.herokuapp.com/"),
      messages: [],
      listUsers: {
        action: "person",
        title: "Users playing",
        users: [
        ]
      }
    };
  },
  methods: {
    clickMe() {
      this.socket.emit("SEND_MESSAGE", {
        user: this.username,
        message: this.message
      });
      this.message = "";
    }
  },
  created() {
    this.username = this.$route.params.username;
    // if(this.username == null){
    //   router.push({name:'home'});
    // }
  },
  mounted() {
    this.socket.on("MESSAGE", data => {
      this.messages = [...this.messages, data];
    });
  }
};
</script>
