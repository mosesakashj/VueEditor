<template>
  <div>
      <template v-for="(i, index) in content" >
        <v-menu offset-y open-on-hover :key="index" v-if="i.menu">
          <template v-slot:activator="{ on, attrs }">
            <v-btn v-bind="attrs" v-on="on" small tile ><v-icon>{{i.icon}}</v-icon> </v-btn>
          </template>
          <v-list height="200">
            <v-list-item v-for="(item, i) in i.menu" :key="i">
              <v-list-item-title @click="item.click">{{ item.text }}</v-list-item-title>
            </v-list-item>
          </v-list>
        </v-menu>
        <v-btn @click="i.click()" :key="index" v-else-if="i.icon" tile small :color="i.active ? 'primary' : ''"><v-icon>{{i.icon}}</v-icon> </v-btn>
        <v-btn @click="i.click()" :key="index" v-else-if="i.html" v-html="i.html" tile small :color="i.active ? 'primary' : ''"></v-btn>
      </template>
  </div>
</template>

<script>
export default {
    props: [ 'content' ]
}
</script>

<style>

</style>