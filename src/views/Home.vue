<template>
  <v-app>
    <Menu/>
    <div>
      <v-container>
        <v-layout row>
          <v-flex xs12 md6 offset-md3>
            <v-alert
              :value="showError"
              color="error"
              icon="warning"
              outline
            >Room Name or password incorrect</v-alert>
          </v-flex>
        </v-layout>
      </v-container>
    </div>
    <v-card class="mx-auto mt-5 pa-5" color="#26c6da" min-width="600">
      <v-form ref="form">
        <v-layout>
          <v-flex xs12>
            <v-text-field v-model="nameRoom" label="Room Name" required></v-text-field>
            <v-flex xs12>
              <v-text-field
                v-model="passwordRoom"
                :append-icon="show1 ? 'visibility' : 'visibility_off'"
                :type="show1 ? 'text' : 'password'"
                name="password"
                label="Password"
                @click:append="show1 = !show1"
              ></v-text-field>
            </v-flex>
            <v-card class="mx-auto mt-5 pa-5" min-width="600">
              <h2>Enter Username or anon</h2>
              <v-layout>
                <v-flex xs12>
                  <v-text-field v-model="username" label="Username" required></v-text-field>
                </v-flex>
              </v-layout>
            </v-card>
            <v-btn @click="enterGame" depressed large>Enter</v-btn>
          </v-flex>
        </v-layout>
      </v-form>
    </v-card>
  </v-app>
</template>

<script>
// @ is an alias to /src
import Menu from "@/components/Menu";
import router from "../router";
import { setTimeout } from "timers";

export default {
  name: "home",
  components: {
    Menu
  },
  data() {
    return {
      nameRoom: "",
      passwordRoom: "",
      username: "",
      show1: false,
      showError: false,
      rules: {
        required: value => !!value || "Required.",
        min: v => v.length >= 8 || "Min 8 characters"
      }
    };
  },
  methods: {
    enterGame: function() {
      const passwordHolder = "momos";
      const nameHolder = "momos";

      if (this.passwordRoom === "momos" && this.nameRoom === "momos") {
        if(this.username.length > 0 && this.username != null){
          router.push({ name: 'game', params: { username: this.username } });
        }else{
          const user = `anon${Math.floor(Math.random() * 1000)}`;
          router.push({ name: 'game', params: { username: user } });
        }
      } else {
        this.showError = !this.showError;
        setTimeout(() => {
          this.showError = !this.showError;
          this.$refs.form.reset();
        }, 3000);
      }
    }
  }
};
</script>
