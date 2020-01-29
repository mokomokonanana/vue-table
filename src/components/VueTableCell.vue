<template lang="pug">
  .col(:style="style" :class="cssClass")
    slot
      template(v-if="row && field") {{ row[field] }}
</template>

<script>
export default {
  props:{
    field: String,
    width: Number,
    row: Object,
    stickyLeft: Number
  },
  computed:{
    style: function(){
      const style = {}
      if(this.stickyLeft != undefined){
        style.position = 'relative'
        style.left = `${this.stickyLeft}px`
      }
      if(this.width) style.width = `${this.width}px`
      return style
    },
    cssClass: function(){
      const cssClass = {}
      if(this.stickyLeft != undefined) cssClass.sticky = true
      return cssClass
    }
  },
}
</script>

<style lang="stylus" scoped>
  .col.sticky
    position relative
    z-index -1
  .col:not(.sticky)
    z-index -2
</style>
