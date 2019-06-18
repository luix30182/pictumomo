<template>
  <v-app>
    <Menu/>
    <div class="border-wrap">
      <canvas id="draw"></canvas>
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

export default {
  name: "Game",
  components: {
    Menu,
    Message
  },
  data() {
    return {
      username: "",
      message: "",
      loading: false,
      socket: io("https://git.heroku.com/pictumomo-chat.git"),
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
    if(this.username == null){
      router.push({name:'home'});
    }
  },
  mounted() {
    this.socket.on("MESSAGE", data => {
      this.messages = [...this.messages, data];
    });

    // this.listUsers = this.messages.map(message => {
    //   return message.user;
    // });

    const canvas = document.querySelector("#draw");
    const ctx = canvas.getContext("2d");
    canvas.width = window.innerWidth;
    canvas.height = window.innerHeight / 2;
    ctx.strokeStyle = "#BADA55";
    ctx.lineJoin = "round";
    ctx.lineCap = "round";
    ctx.lineWidth = 5;
    let isDrawing = false;
    let lastX = 0;
    let lastY = 0;
    let hue = 0;
    // let direction = true;
    function draw(e) {
      if (!isDrawing) return; // stop the fn from running when they are not moused down
      ctx.strokeStyle = `hsl(${hue}, 100%, 50%)`;
      ctx.beginPath();
      // start from
      ctx.moveTo(lastX, lastY);
      // go to
      if (e.type == "touchmove") {
        const rect = e.target.getBoundingClientRect();
        const x = Math.floor(e.targetTouches[0].pageX) - rect.left;
        const y = Math.floor(e.targetTouches[0].pageY) - rect.top;
        ctx.lineTo(x, y);
        [lastX, lastY] = [x, y];
      } else {
        ctx.lineTo(e.offsetX, e.offsetY);
        [lastX, lastY] = [e.offsetX, e.offsetY];
      }
      ctx.stroke();
      hue++;
      if (hue >= 360) {
        hue = 0;
      }
    }
    canvas.addEventListener("mousedown", e => {
      isDrawing = true;
      [lastX, lastY] = [e.offsetX, e.offsetY];
    });

    const touchAvailable =
      "createTouch" in document || "ontouchstart" in window;

    if (touchAvailable) {
      canvas.addEventListener("touchmove", draw, false);
      canvas.addEventListener("touchstart", () => (isDrawing = false), false);
      canvas.addEventListener("touchend", () => (isDrawing = false), false);
      canvas.addEventListener("touchstart", e => {
        isDrawing = true;
        const rect = e.target.getBoundingClientRect();
        const x = Math.floor(e.targetTouches[0].pageX) - rect.left;
        const y = Math.floor(e.targetTouches[0].pageY) - rect.top;
        [lastX, lastY] = [x, y];
      });
    } else {
      canvas.addEventListener("mousemove", draw);
      canvas.addEventListener("mouseup", () => (isDrawing = false));
      canvas.addEventListener("mouseout", () => (isDrawing = false));
    }

    // document.body.addEventListener ("touchmove", function (e) { e.preventDefault (); }, {passive: false});
  }
};
</script>
