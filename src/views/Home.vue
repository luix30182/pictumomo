<template>
  <v-app>
    <Menu/>
    <v-container>
      <v-form>
        <v-layout row wrap>
          <v-flex xs12>
            <v-text-field v-model="username" label="Nickname" outline clearable></v-text-field>
          </v-flex>
          <v-flex xs12>
            <v-text-field v-model="nameRoom" label="Room" outline clearable></v-text-field>
          </v-flex>
          <v-flex xs12>
            <v-text-field
              v-model="passwordRoom"
              :append-icon="show1 ? 'visibility' : 'visibility_off'"
              :type="show1 ? 'text' : 'password'"
              label="Password"
              outline
              clearable
              @click:append="show1 = !show1"
            ></v-text-field>
          </v-flex>
          <v-flex xs12>
            <div class="text-xs-center">
              <v-btn @click="enterGame" block outline color="indigo">Play!</v-btn>
            </div>
          </v-flex>
          <v-flex xs12>
            <v-alert :value="showError" color="error" icon="warning" outline>Password or Room name error</v-alert>
          </v-flex>
        </v-layout>
      </v-form>
    </v-container>
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
      if (
        this.passwordRoom === passwordHolder &&
        this.nameRoom === nameHolder
      ) {
        if (this.username.length > 0 && this.username != null) {
          router.push({ name: "game", params: { username: this.username } });
        } else {
          const user = `anon${Math.floor(Math.random() * 1000)}`;
          router.push({ name: "game", params: { username: user } });
        }
      } else {
        this.showError = !this.showError;
        setTimeout(() => {
          this.showError = !this.showError;
          this.$refs.form.reset();
        }, 1000);
      }
    }
  }
};
</script>
