<template lang="pug">
  .col(:style="style")
    slot
      template(v-if="row && field") {{ row[field] }}
</template>

<script>
export default {
  props:{
    field: String,
    width: Number,
    row: Object
  },
  computed:{
    style: function(){
      const style = {}
      if(this.width) style.width = `${this.width}px`
      return style
    }
  },
}
</script>

<style lang="stylus" scoped>
</style>
