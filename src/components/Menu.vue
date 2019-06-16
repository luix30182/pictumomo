<template>
  <v-toolbar>
    <v-toolbar-title class="icon">Pictu<span>momos</span></v-toolbar-title>
    <v-spacer></v-spacer>
  </v-toolbar>
</template>
<style scoped>
.icon{
  text-transform: uppercase;
}
.icon span{
  background: -webkit-linear-gradient(rgb(0, 110, 255), rgb(60, 161, 255));
  -webkit-background-clip: text;
  -webkit-text-fill-color: transparent;
}
</style>

<script>
export default {
  name: "Menu"
};
</script>
