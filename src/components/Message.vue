<template>
  <v-layout row>
    <v-flex xs4 sm6 offset-sm3>
      <h4 class="blue--text">{{message.user}}: </h4>
    </v-flex>
    <v-flex xs8>
      <p>{{message.message}}</p>
    </v-flex>
  </v-layout>
</template>
<script>
export default {
    name:'message',
    props: ['message'],
    created(){
    }
}
</script>

