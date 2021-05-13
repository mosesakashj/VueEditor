<template>
  <div class="px-5" style="border: 1px solid black">
      <template v-for="(i, index) in content" >
        <v-menu offset-y open-on-hover :key="index" v-if="i.menu">
          <template v-slot:activator="{ on, attrs }">
            <v-btn v-bind="attrs" v-on="on" small tile :disabled="i.disabled"><v-icon>{{i.icon}}</v-icon> </v-btn>
          </template>
          <v-list max-height="200">
            <v-list-item v-for="(item, i) in i.menu" :key="i">
              <v-list-item-title @click="item.click">{{ item.text }}</v-list-item-title>
            </v-list-item>
          </v-list>
        </v-menu>
        <v-tooltip bottom  v-else-if="i.icon"  :key="index">
          <template v-slot:activator="{ on, attrs }">
          <v-btn @click="i.click()" tile small :color="i.active ? 'primary' : ''" :disabled="i.disabled" v-bind="attrs" v-on="on"><v-icon>{{i.icon}}</v-icon> </v-btn>
          </template>
          <span>{{i.title}}</span>
        </v-tooltip>
        <v-btn @click="i.click()" :key="index" v-else-if="i.html" v-html="i.html" tile small :color="i.active ? 'primary' : ''" :disabled="i.disabled"></v-btn>
        <input class="color-apply" :disabled="i.disabled" :key="index" type="color" @change="i.update_color(selectedColor)" v-model="selectedColor" v-if="i.is === 'button-color'">
      </template>
  </div>
</template>

<script>
export default {
    props: [ 'content' ],
    data () {
      return {
        selectedColor: ''
      }
    }
}
</script>

<style>

</style>