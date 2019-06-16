<template>
  <v-app>
    <Menu/>
    <div class="border-wrap">
      <canvas id="draw"></canvas>
      <div class="separator"></div>
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
.separator{
  height: 3px;
  width: 100%;
  background: linear-gradient(blue,cyan);
}
</style>

<script>
// @ is an alias to /src
import Menu from "@/components/Menu";
import Message from "@/components/Message";
import io from "socket.io-client";

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
      // socket: io("https://pictumomo.herokuapp.com/"),
      messages: [],
      listUsers: []
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
  },
  mounted() {
    // this.socket.on("MESSAGE", data => {
    //   this.messages = [...this.messages, data];
    // });

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
        console.log(x,y)
        ctx.lineTo(x,y);
        [lastX, lastY] = [x,y];
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
        [lastX, lastY] = [x,y];
      });
    } else {
      canvas.addEventListener("mousemove", draw);
      canvas.addEventListener("mouseup", () => (isDrawing = false));
      canvas.addEventListener("mouseout", () => (isDrawing = false));
    }

    document.body.addEventListener ("touchmove", function (e) { e.preventDefault (); }, {passive: false});
  }
};
</script>
