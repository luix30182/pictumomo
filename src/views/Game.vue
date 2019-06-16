<template>
  <v-app>
    <Menu/>
    <div class="border-wrap">
      <canvas id="draw" width="800" height="800"></canvas>
    </div>
    <v-container>
      <v-layout row wrap>
        <v-flex xs12 md3>
          <h3>List of players</h3>
        </v-flex>
        <v-flex xs12 md8 offset-md1>
          <div class="border-wrap-chat">
            <div id="chat">
              <h3>Hello {{username}}</h3>
              <Message/>
              <v-layout row wrap>
                <v-flex xs12 md8 offset-md2>
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
              </v-layout>
            </div>
          </div>
        </v-flex>
      </v-layout>
    </v-container>
  </v-app>
</template>

<style scoped>
.border-wrap {
  position: relative;
  background: linear-gradient(to right, blue, cyan);
}
.border-wrap-chat {
  position: relative;
  background: linear-gradient(to right, blue, cyan);
  padding-left: 5px;
  width: 100%;
}
canvas,
#chat {
  background: #fff;
}
</style>

<script>
// @ is an alias to /src
import Menu from "@/components/Menu";
import Message from "@/components/Message";
import router from "../router";

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
      items: [
        {
          avatar: "https://cdn.vuetifyjs.com/images/lists/1.jpg",
          title: "Brunch this weekend?",
          subtitle:
            "<span class='text--primary'>Ali Connors</span> &mdash; I'll be in your neighborhood doing errands this weekend. Do you want to hang out?"
        }
      ]
    };
  },
  methods: {
    clickMe() {
      this.loading = true;
      this.message = "Wait for it...";
      setTimeout(() => {
        this.loading = false;
        this.message = "You've clicked me!";
      }, 2000);
    }
  },
  created() {
    this.username = this.$route.params.username;
  },
  mounted() {
    const chat = document.querySelector("#chat");
    chat.height = window.innerHeight / 2;
    const canvas = document.querySelector("#draw");
    const ctx = canvas.getContext("2d");
    canvas.width = window.innerWidth;
    canvas.height = window.innerHeight / 2;
    ctx.strokeStyle = "#BADA55";
    ctx.lineJoin = "round";
    ctx.lineCap = "round";
    ctx.lineWidth = 10;
    // ctx.globalCompositeOperation = 'multiply';
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
      ctx.lineTo(e.offsetX, e.offsetY);
      ctx.stroke();
      [lastX, lastY] = [e.offsetX, e.offsetY];
      hue++;
      if (hue >= 360) {
        hue = 0;
      }
    }
    canvas.addEventListener("mousedown", e => {
      isDrawing = true;
      [lastX, lastY] = [e.offsetX, e.offsetY];
    });
    canvas.addEventListener("mousemove", draw);
    canvas.addEventListener("mouseup", () => (isDrawing = false));
    canvas.addEventListener("mouseout", () => (isDrawing = false));
  }
};
</script>
