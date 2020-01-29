<template lang="pug">
  div(:class="{col: !child, column: child, last: !child }" :style="colStyle")
    template(v-if="!child" ) {{ label }}
    template(v-else)
      div(class="col") {{ label }}
      .row
        VueTableHeaderCol(v-for="(col, index) in child" :key="index" v-bind="col")
</template>

<script>
import VueTableHeaderCol from '@/components/VueTableHeaderCol.vue'

export default {
  name: 'VueTableHeaderCol',
  components:{ VueTableHeaderCol },
  props:{
    label: String,
    child: Array,
    width: Number,
    stickyLeft: Number
  },
  computed:{
    // スタイル調整
    colStyle: function(){
      const style = {}
      if(this.stickyLeft){
        style.position = 'relative'
        style.left = `${this.stickyLeft}px`
      }
      if(this.width) style.width = `${this.width}px`
      return style
    },
  }
}
</script>