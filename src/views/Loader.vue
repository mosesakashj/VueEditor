<template>
  <div>
    <v-img src="https://flevix.com/wp-content/uploads/2019/12/Quarter-Circle-Loading-Image-1.gif" :height="height" :width="width"></v-img>
  </div>
</template>

<script>
export default {
  data () {
    return {
      height: window.innerHeight,
      width: window.innerWidth
    }
  }
}
</script>

<style>

</style>