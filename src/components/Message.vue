<template>
  <div>
    <v-flex xs12 sm6 offset-sm2>
      <v-card>
        <v-list two-line>
          <template>
            <v-list-tile avatar>
              <v-list-tile-avatar>
                <img
                  src="https://png.pngtree.com/element_origin_min_pic/12/03/20/1656e3909271e51.jpg"
                >
              </v-list-tile-avatar>

              <v-list-tile-content>
                <v-list-tile-title v-html="'user'"></v-list-tile-title>
                <v-list-tile-sub-title v-html="'message'"></v-list-tile-sub-title>
              </v-list-tile-content>
            </v-list-tile>
          </template>
        </v-list>
      </v-card>
    </v-flex>
  </div>
</template>

<script>
export default {
    name:'message'
}
</script>

